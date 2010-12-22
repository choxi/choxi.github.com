---
  title: First Post
  layout: default
---

{% highlight ruby %}
describe Artist do
  it "has a unique email address" do
    Factory(:user, :email => "jeff@topnotch.com")
    lambda { Factory(:user, :email => "jeff@topnotch.com") }.should
    raise_error(ActiveRecord::RecordInvalid)
  end
end
{% endhighlight %}

