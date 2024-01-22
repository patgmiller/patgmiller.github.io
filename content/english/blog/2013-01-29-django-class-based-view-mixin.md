---
title: Django Class Based Views + Mixins
author: Patrick Miller
date: "2013-01-29T01:38:18Z"
draft: false
categories:
- Programming
tags:
- python
- django
- composition
---
For the pass two years I've been developing in Python with the [Django](https://www.djangoproject.com/) framework and I have thoroughly enjoyed. As much as possible I have attempted to build tools that follow the DRY, Don't Repeat Yourself, principle. With Django 1.3+ they released [Classed Based Views](https://docs.djangoproject.com/en/dev/topics/class-based-views/) Classed Based Views</a> (CBV), which, in my opinion lend themselves more to generic applications than do the [Function Based Views](https://docs.djangoproject.com/en/dev/topics/http/views/) Function Based Views</a> (FBV).
<!--more-->

There are varied opinions on the Internet regarding the pros and cons of the two methods. Generallyarguments are out that FBV's are simpler, easier to maintain, and troubleshoot while being somewhat rigid in application and the CBV's are complex, harder to maintain, and troubleshoot, while being highly flexible therefore very reusable in different applications. In my experience I have found most of that to be correct, that being said I have used the FBV and then put some time into learning the CBV and the end result is that I greatly prefer the CBV's over the FBV's.

One way to describe my reasoning might be to compare Calculus to simpler methods of Math. For certain problems you could use either one to produce a solution, but just because one has difficulty understanding higher concepts doesn't detract from the power or the effectiveness of them in solving problems.

## List View Filter Mixin

I see class mixins as simply another CBV, which has no direct knowledge of the objects that it will interacte with, and which can be used with one or more CBV's to extend, or enhance the default CBV's and produce more dynamic views. Here is one example of a filter mixin I built, with examples in the doc string.
{{< highlight python >}}
class FilterMixin(object):
    """
    Mixin to filter or exclude the queryset. The mixing will check for an
    attribute on the current class View (self) and then in the kwargs.
    #===========================================================================
    # Example (class attribute):
    #===========================================================================
    # app/views.py
    class ProfileMixin(object):
        profile_field = 'user__username'
        profile_url_kwarg = 'profile'
        owner_required = False
        login_required = False
        def dispatch(self, request, *args, **kwargs):
            self.profile = get_object_or_404(Profile,
                                    **{ self.profile_field : \
                                       kwargs.get(self.profile_url_kwarg, None) })
            if self.login_required and not request.user.is_authenticated():
                return HttpResponseRedirect(reverse('account_login') \
                                                     + "?next=" \
                                                     + request.path_info)
            if self.owner_required:
                if self.profile != request.user.get_profile():
                    raise Http404
            return super(ProfileMixin, self).dispatch(request, *args, **kwargs)
    class ProfileRelatedListView(ProfileMixin, FilterMixin, ListView):
        pass
    # app/urls.py
        url(r'^(?P
<profile>[\w-]+)/$',
            ProfileRelatedListView.as_view(model=WallEntry,
                                          filter={'wall__profile':'profile'},
                                          template_name="app/profile_detail.html",
                                          page_template="app/profile_detail_page.html"),
    #===========================================================================
    # Example (kwargs):
    #===========================================================================
    # app/views.py
        class MyListView(FilterMixin, ListView):
            pass
    # app/urls.py
        url(r'^(?P<username>[\w-]+)/$',
            MyListView.as_view(model=Photos,
                               filter={'user__username':'username'},)),
    """
    filter = {}
    exclude = {}
    def get_queryset(self):
        filter = {}
        exclude = {}
        for k,v in self.exclude.iteritems():
            value = getattr(self, v, None) or self.kwargs.get(v, None)
            exclude[k] = value if value else v
        for k,v in self.filter.iteritems():
            value = getattr(self, v, None) or self.kwargs.get(v, None)
            filter[k] = value if value else v
        if self.queryset:
            self.queryset = self.queryset.filter(**filter).exclude(**exclude)
        else:
            self.queryset = super(FilterMixin, self).get_queryset() \
                                  .filter(**filter).exclude(**exclude)
        return self.queryset
{{< / highlight >}}
