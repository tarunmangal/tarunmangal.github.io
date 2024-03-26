---
layout: post
title:  "Welcome to Jekyll!"
date:   2014-08-29 14:34:25
categories: jekyll update
tags: featured
image: /assets/article_images/2014-08-29-welcome-to-jekyll/desktop.JPG
---
# Designing a Master Data Management System (MDMS) for the real world - Part 1
---
I feel very strongly about this topic. However, it was not always like that. How is it any different from building any other complex system? I personally never thought much about master data mgmt systems. Until I came across this [online book](https://abseil.io/resources/swe-book/html/toc.html) (thanks Aameer!). In chapter 4 of this book, the author talks about the lifetime of data and how this one factor can have unforeseen consequences on the maintainability and usefulness of a system. It completely changed my perspective on how to view and judge the design of a system. There is one system in particular that piqued my interest. It sits at the core of any e-commerce stack, the catalog management system, or CMS.

In this article, I will use CMS as a special use-case and try to derive generic design aspects that are important while designing any MDMS.
## Designing an MDMS
### Important Functional Considerations
1. The contract and relationship between the entities have to be correct at all times. Really!! 😊
2. The life of an entity can vary from months to years.
3. The value of the attributes may be incorrect. The incorrect values can be discovered at any point during the lifetime of an entity. There should be support for correcting the values and managing its side effects. The cost of correction is an important consideration.
4. When an entity gets created it may have side effects on other entities or systems downstream. These downstream systems and entities can react to events in a sync or async manner.
5. Over time an entity may evolve, like splitting into two new entities, getting merged with another entity, or the definition of the entity changing to accommodate the changing business environment. This can lead to side effects. Based on the change, it is important to ensure that affected downstream entities or systems respond to change either synchronously or asynchronously.
6. **The system(s) have to reach a consistent state as a whole.**
7. Based on the business, any change or creation of an entity may or may not be visible immediately. This is normally (not necessarily) going to happen when the side effects take place asynchronously. _(A good question to ask is, whether it's the responsibility of the central MDMS to ensure all downstream systems reach a consistent state w.r.t the change or is it the other way around? We will cover this later.)_
8. The contracts with the dependent systems should allow self-checking to ensure the system has reached a consistent state. There should be a strategy to recover from an inconsistency. Solutions, can range from bubbling up the scenario so that it can be taken up offline to rolling back the changes.
9. It is important to capture the who, the what, the when, and with who's authorisation, an entity was created, edited, or archived.
10. It is important to build the explainability of every mutation. It is also important from an audit perspective.
11. Deleting an entity is non-trivial. It depends on the system and prevailing laws etc. It can lead to potential Controls risks downstream.

To understand these design considerations, we will take the CMS as a reference point.
> Let me take a short sidebar and give a very high-level introduction of CMS - 
> 
> #### What is a CMS? 
> For any e-commerce company, its catalog is one of the fundamental building blocks. Almost all the other services, in some way, depend on the catalog to carry out their business.
>  
>  
>  
>  
> A catalog is comprised of the following entities. Each of these entities are only high-level concepts. Their actual implementation can change from business to business.\
> A definition of a product. A product represents an entity that can have a physical manifestation, like toothpaste, or a service, like insurance.\
> The definition of a product usually confirms with a predefined template. It is also sometimes called vertical. 
> A vertical defines the template of a given class of similar products. It essentially consists of attributes that define a given product. The value of these attributes can be open text, enumerated, list-type, image-type, etc. These attributes can belong to different sub-templates like tax-specific attributes, batch-specific attributes, or physical-details-specific attributes. 
> All the products can be grouped into something called a category tree. However, this category tree is specific to the consumer. For example, the customer-facing category tree will group products in a fashion that allows customers to navigate the tree easily and find the right product ASAP. Whereas, the business teams would like to view the category tree based on how the business teams have structured themselves internally.
> Along with the category tree, there can be many other ways through which one would like to group these products. For example in clothing, there can be multiple variants. One variant can be based on size, while the other can be based on colour. The customer needs to view all of them together to ease discovery. The idea of variant needs to be very fluid as the needs can be different for different classes of products. 
> All in all, it is a fairly complex interaction of various entities. The reality is more twisted and complex, but for the discussion, the above set of definitions will suffice.\

### Entity creation

You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve --watch`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll’s dedicated Help repository][jekyll-help].

{% highlight js %}

<footer class="site-footer">
 <a class="subscribe" href="{{ "/feed.xml" | prepend: site.baseurl }}"> <span class="tooltip"> <i class="fa fa-rss"></i> Subscribe!</span></a>
  <div class="inner">a
   <section class="copyright">All content copyright <a href="mailto:{{ site.email}}">{{ site.name }}</a> &copy; {{ site.time | date: '%Y' }} &bull; All rights reserved.</section>
   <section class="poweredby">Made with <a href="http://jekyllrb.com"> Jekyll</a></section>
  </div>
</footer>
{% endhighlight %}


[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help
