---
title: Sliding AJAX Photo Browser
author: Patrick Miller
date: "2012-01-06T12:49:44Z"
draft: false
categories:
- Programming
tags:
- javascript
- jquery
- ajax
# image: /images/2012/01/Buy-Print-Photo-Browser-Basic-300x115.png
summary: Above is the original photo browser for the print details page on any Instaproofs photographer website. It highlighted the previous, current, and next photo for an event category and the previous and next images were also links to the actual print details page and if clicked would result in a new page request. Very clean, simple, and effective. You could browse the prints quicker with a search or category page, which also resulted in a new page request.
---
### Original Photo Browser

![Buy Print Photo Browser - Basic](/images/2012/01/Buy-Print-Photo-Browser-Basic-300x115.png)

Above is the original photo browser for the print details page on any Instaproofs photographer website. It highlighted the previous, current, and next photo for an event category and the previous and next images were also links to the actual print details page and if clicked would result in a new page request. Very clean, simple, and effective. You could browse the prints quicker with a search or category page, which also resulted in a new page request.

### Sliding AJAX Photo Browser

![Buy Print Sliding AJAX Photo Browser](/images/2012/01/Buy-Print-Sliding-AJAX-Photo-Browser-300x113.png)

![Buy Print Sliding AJAX Photo Browser - Next](/images/2012/01/Buy-Print-Sliding-AJAX-Photo-Browser-Next1-300x113.png)

This is the updated photo browser for the print details page. Initially, the photo browser looks identical to the original version. When the page is loaded several additional print thumbnails from the event category are included in the hidden overflow. Then as you mouse over anywhere on the photo browser the previous and next buttons show up without red background and then turn red as the mouse cursor moves over each button respectively.

Clicking on either button, using JavaScript ( jQuery ), the viewing widow slides in the direction indicated and request up to two additional print thumbnails, via an AJAX call ( jQuery ), which are also links to the actual print details page shown.

## History

This was a task from 2011 that I did for [instaproofs.com](https://instaproofs.com).
