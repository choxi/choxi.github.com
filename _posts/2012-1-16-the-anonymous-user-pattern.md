---
  title: The Anonymous User Pattern
  layout: post
---

## Anonymous Users on Stripe
One of the features of Stripe that really impressed me on my first
visit was, since I
don't know if it has an established name, what I'll call the Anonymous User
pattern. If you go to [Stripe.com](http://stripe.com) and press "Get Started with Stripe",
you can try out a fully-functional Stripe account **without ever
entering a username, email address, or password.** Now, allowing users
to demo your product before signing up isn't anything new, but Stripe
took it to the extreme of allowing you to use every single feature that
a registered user could use without signing up. You even had access to
the API keys and secrets and could start sending requests to Stripe.

![](/images/stripe_anonymous_users.jpg)

There are enough studies that make a convincing case for allowing
users to "try before they buy", [Unbounce was able to increase their
conversion rates by 12%](http://unbounce.com/lead-generation/conversion-psychology-1-why-stealing-grapes-increases-form-conversions-case-study/) using this model. The idea is to earn the trust of your users with a minimal
investment on their part, **you want to ease them along the funnel instead
of requiring the large upfront cost of giving up their email address and
creating another online account to keep mental note of**.
But, this post is more of a technical
one on how to implement the pattern and
allow users to try out a fully-functioning version of your product
before signing up.

## Old Solution: Tracking State in Cookies

In general, the difficult part in allowing anonymous usage is that, for
every feature you want to support, you have to consider both scenarios:
a logged-in user as well as an anonymous user. I'll use Bloc as an example.
Users on Bloc can earn badges, build apps, and complete
courses and challenges. We want to allow them to do this without signing
up, but how do we save their badges, apps, and course progresses if we
can't rely on a User record to tie it to? If it were just one of those
things, it might be fairly easy to just save an App id or Badge id in
a session or as it's own cookie. But when it's all three of those, and
each user can have multiple instances of each, it gets to be very
cumbersome to 1) save the state of dozens of records in cookies and 2) modify all of our
backend to support both anonymous users (by looking up Apps, Badges, and
CourseProgresses via cookies) as well as a logged in "current user". You
end up with code like this:

{% highlight ruby %}
# user_badges_controller.rb

def create
  if user_signed_in?
    current_user.user_badges.create(:badge => earned_badge)
  else
    # Have to create the badge later if the user registers.
    # Save it in a cookie in the meantime.
    cookies[:earned_badge] = earned_badge.id
  end
end
{% endhighlight %}

This looks reasonably innocuous, but if you multiply it by 20 for all the features that have
to branch based on the user's login status it changes from the easy solution to the solution
that's slowing down development because of the two scenarios you
have to consider for each new feature.

## New Solution: Delegating to a Shared Record
The more elegant solution we came up with was to introduce a new table of
Identities that optionally belongs to a User -- Apps/Badges/Courses
are then tied to an Identity instead of a User. When the user is
logged in, we fetch the Identity from the logged in User record. When there is no
logged in user, we create an Identity record and save its id in the
session.

**Warning:** If you use a cookie-based session, you have to either sign
the cookie or verify that the Identity does not already belong to a user
when you're fetching an Identity for an anonymous user. Otherwise it's
trivial for a hacker to spoof as any other user. The solution in Rails
below should be adequate.

{% highlight ruby %}
# application_controller.rb

helper_method :current_identity

def current_identity
  if cookies.signed[:identity_id].present?
    identity = Identity.find(cookies.signed[:identity_id])
  else
    identity = Identity.create
    cookies.signed[:identity_id] = identity.id
  end

  return identity
end
{% endhighlight %}

{% highlight ruby %}
# user_badges_controller.rb

def create
  current_identity.user_badges.create(:badge => earned_badge)
end
{% endhighlight %}

The beauty of this is that none of your other backend or database schema
has to know anything about whether the user is anonymous or not, it can
continue to function normally. Syncing also becomes trivial -- if a user
starts out using your product anonymously and then registers,
synchronizing only means you have to set the newly registered User on
the Identity that you've been tracking via the session.

{% highlight ruby %}
# registrations_controller.rb

def create
  ... # create the new User record
  user.update_attributes(:identity => current_identity)
end

{% endhighlight %}

**Followup:** I'm a firm believer in the Lean/Scientific Method of developing
features so I'm a little ashamed that I don't have metrics to support
the anonymous user pattern. I'll try to find the time to retroactively
add a test in and do a follow up post.
