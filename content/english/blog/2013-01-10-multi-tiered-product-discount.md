---
title: PHP Decorator Class
author: Patrick Miller
date: "2013-01-10T21:51:33Z"
draft: false
categories:
- Programming
tags:
- php
- design patterns
- decorator
- ecommerce
---
Last year I was given the challenge of adding multiple tiered discounts, simply put as bulk discounts on a large scale custom shopping cart system. The goal was to provide shop owners with the ability to give their customers discounts for buying any product in bulk and that could be configured on a per-product basis.

This shopping cart system hosted multiple stores,upwards of around 30,000 users and grows each month by several hundred. Also each product is setup in a group, when enabled these discounts could be one of two types: Fixed, Compound.
<!--more-->

## Tiers
As requested by the client the product tier was to account for a quantity and a price at that quantity. The first tier is always assumed to be at a quantity of 1 and the price is the actual product price.

For example, Let's say product A cost $5.00, so Tier 1 is Qty: 1 Price: $5.00, and the following could be additional tiers - qty:10 and price:$4.50, qty:50 and price:$4.00. So the resulting tiers would be:

* Qty: 1, Price: $5.00
* Qty: 10, Price: $4.50
* Qty: 50, Price: $4.00

## Discount Types: Fixed vs. Compound
A fixed discount type would provide the customer with the each product at a single discount price where the compound only gets the discount, price break, for quantity at each tier.

For example, using our previous tiers, lets say a customer purchased a 11 items of product A.

* Fixed discount is: `11 x $4.50 = $49.50`
* Compound is: `9 x $5.00 + 2 x $4.50 = $54.00`

Which discount you use would depend on the product, the compound in this case was meant for custom digital products where most of the work was done on the first item, and any additional items were easy to produce, so the second tier usually started at a quantity of 2 items.

## Implementation
Sounds easy, but remember this was an in house shopping cart system, and a user shop owner base of around 30,000, so just imagine how many products there could be, so you've also got to make sure you're writing fast database queries, and allow for testing in a beta and production environment.

### Approach
The basic idea I wanted to achieve was to write some class decorators that would add this discount functionality to the cart and order classes with having to make very minimal changes the original classes. The coding environment was PHP 5.3 / MySQL. The overview of my approach was this:

* Use a namespace
* Build a single abstract class Dectorator
* Use magic methods: __call, __get
* Two concrete clases Cart and Order

### Code
{{< highlight php >}}
<?
namespace Decorator;
/* the required files with previously defined classes */
use \Cart as CoreCart;
abstract class Decorator {
	/**
	 * Decorated object
	 * @var object Reference to wrapped object
	 */
	protected $obj;
	function __construct($obj = null) {
		if(is_object($obj)) {
			$this->obj = $obj;
		} else {
			$this->obj = new stdClass();
		}
	}
	/**
	 * On Missing Method
	 *
	 * When the decorator object does not have the called method implimentation attempt to call the wrapped/decorated object
	 *
	 * Pre/Post Method call processing. If you need to modify any of the data before or after any method calls, then you may
	 * declare a function for the class Decorator in this manner:
	 *
	 *   function PreHookMethodNameHere($arg1, $arg2, .... $argN)
	 *   		or
	 *   function PreHookMethodNameHere() {
	 *   	$args = func_get_ar
	 *   		...
	 *   	return;
	 *   }
	 *
	 * @param string $method
	 * @param array $args
	 */
	function __call($method, $args = array()) {
		/**
		 * PreHook MethodCalls
		 *
		 * preMethod function delcaration must either match called method or get_func_args().
		 * @var array
		 */
		if(method_exists($this, "PreHook".$method)) {
			//$args[] = 'func_get_args::test';
			call_user_func_array(array($this, "PreHook".$method), $args);
		}
		try {
			$results = call_user_func_array(array($this->obj, $method), $args);
		} catch(Exception $e) {
			throw new Exception("Call to missing method ".get_class($this->obj)."::$method()" );
		}
		/**
		 * PostHook Method Calls
		 *
		 * The results of the called ojbect method will be pushed on the original $args array.
		 * postMethod function declaration should use get_func_args() and pop called method results off
		 * then set any defaults of $args based on position if necessary
		 * @var array
		 */
		if(method_exists($this, "PostHook".$method)) {
			$args[] = $results;
			$results = call_user_func_array(array($this, "PostHook".$method), $args);
		}
		return $results;
	}
	//provide access to decorated class objects
	function __get($attr) {
		try {
			return $this->obj->$attr;
		} catch(Exception $e) {
			throw new Exception(get_class($this->obj)." has no member attribute '$attr'" );
		}
	}
}
/**
 * Implement BulkPricing
 *
 * Transparent Wrapper class to add bulkPricing for Cart and Order. Also provide a very easy method of disabling
 * this functionality, just comment out namespace declaration in pages that are declaring the cart like so:
 * 		use \Cart as CoreCart;
 * 		use \Decorator\Cart as Cart;
 * @author pgmiller
 */
abstract class BulkPricingCollectionDecorator extends Decorator {
	protected $collectionId;
	protected $collectionTable;
	protected $collectionColumn;
	protected $collectibleTable;
	protected $collectibleColumn;
	/**
	 * @var array
	 * $collection[$collectible->GetHash()] =>
	 * 		array(	'totalQty'=>(int),
	 * 				'list'=>array('collectibleId'=>$collectible)
	 * 		)
	 */
	protected $collection = array();
	protected $bulkPricingTiers = array();
	function __construct($obj, $configs = array()) {
		parent::__construct($obj);
		$default = array(
			'collectionId'=>0,
		);
		$configs = array_merge($default, $configs);
		$this->SetCollectionId($configs['collectionId']);
	}
	function SetCollectionId($id) {
		$id = filterInt($id);
		if($id)
		{
			$this->collectionId = $id;
			return true;
		}
		else
			return false;
	}
	/**
	 * Build a the bulkPricing detials
	 *
	 * @param $collectionId Primary Key for entire group of objects, in this case the cartId or orderId
	 * @param $eventId
	 * @return returns collection by reference in case it is needed.
	 */
	function GetBulkPricingCollectionArray($collectionId = 0, $timeZoneCode = 'America/Denver')
	{
		if($collectionId = filterInt($collectionId))
			$this->SetCollectionId($collectionId);
		$timeZoneCode = (trim($timeZoneCode) == '') ? 'America/Denver' : trim($timeZoneCode);
		/*
		.... other code here not show
		*/
		return $this->collection;
	}
	function CalcBulkImagePrice($quantity, $bulkPricingTiers, $basePrice = 0)
	{
		$bulkPrice = false;
		if($bulkPricingTiers && is_array($bulkPricingTiers))
		{
			if(filterInt($quantity) >= $bulkPricingTiers[0]->quantity)
			{
				foreach($bulkPricingTiers as $tier)
				{
					if($quantity >= $tier->quantity)
						$bulkPrice = $tier->price;
					else
						break;
				}
			}
		}
		return (!$bulkPrice && $basePrice) ? $basePrice : $bulkPrice;
	}
}
/**
 * Concrete BulkPricing Wrapper
 *
 * Concrete Wrapper class to add bulkPricing for the cart.
 *
 * @author pgmiller
 */
class Cart extends BulkPricingCollectionDecorator {
	function __construct($db, $configs=array()) {
		parent::__construct(new CoreCart($db), $configs);
		$this->collectionTable 		= 'cart';
		$this->collectionColumn 	= 'cartId';
		$this->collectibleTable 	= 'cartItem';
		$this->collectibleColumn 	= 'cartItemId';
	}
	/**
	 * Build and return details of shopping cart
	 *
	 * Because the \CoreCart class calls GetPhotoCartItemList on itself $this-> ... the call bypasses the decorator
	 * class and so a simple solution is to catch the original call and pass the bulkPricing modified photoItems
	 * along with the call.
	 *
	 * @param $cartId
	 * @param $sessionId
	 * @param $eventId
	 * @param $timeZoneCode
	 */
	function GetCart($cartId, $sessionId, $eventId = "0", $timeZoneCode = 'America/Denver')
	{
		//echo "<pre>".print_r(func_get_args(),true)."</pre>";
		$itemList = $this->GetCartItemList($cartId, $eventId, $timeZoneCode);
		return $this->obj->GetCart($cartId, $sessionId, $eventId, $timeZoneCode, $itemList);
	}
	function PreHookGetCartItemList($cartId, $eventId = "1", $timeZoneCode = 'America/Denver') {
		//echo "<pre>".__METHOD__." ".print_r(array($cartId, $eventId, $timeZoneCode),true)."</pre>";
		//echo "<pre>".__METHOD__." ".print_r(func_get_args(),true)."</pre>";
		$this->GetBulkPricingCollectionArray($cartId, $eventId, $timeZoneCode);
	}
	function PostHookGetCartItemList($cartId, $eventId = "1", $timeZoneCode = 'America/Denver') {
		$args = func_get_args();
		$cartItems = array_pop($args); //results of GetCartItemList on top of stack
		$args[] = $cartItems;
		if(is_array($cartItems))
		{
			foreach ($cartItems as $index=>$cartItem)
			{
				/* all cartItem post processing */
			}
		}
		return $cartItems;
	}
}
{{< / highlight >}}
