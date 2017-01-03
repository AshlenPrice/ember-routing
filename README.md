[![General Assembly Logo](https://camo.githubusercontent.com/1a91b05b8f4d44b5bbfb83abac2b0996d8e26c92/687474703a2f2f692e696d6775722e636f6d2f6b6538555354712e706e67)](https://generalassemb.ly/education/web-development-immersive)

# Ember Static Routing

## Prerequisites

-   [ga-wdi-boston/ember](https://github.com/ga-wdi-boston/ember)
-   [ga-wdi-boston/ember-inspector](https://github.com/ga-wdi-boston/ember-inspector)
-   [ga-wdi-boston/ember-object](https://github.com/ga-wdi-boston/ember-object)

## Objectives

By the end of this, developers should be able to:

-   Create new Routes and Templates with generators.
-   Configure the Ember Router to point to a new Template.
-   Use the `{{#link-to}}` helper to route from one view-state to another.

## Preparation

1.  [Fork and clone](https://github.com/ga-wdi-boston/meta/wiki/ForkAndClone)
    this repository.
1.  Install dependencies with `npm install` and `bower install`.
1.  Run the development server with `ember server`. Use the `--proxy` flag to
    avoid writing development-specific CORS and CSP settings.

## Code-Along: Routing to Templates

Start the front-end server with `ember serve`, and open the application by
navigating to `http://localhost:7165`.

When you do, you should see a page that looks like this:

!['application' View](https://cloud.githubusercontent.com/assets/388761/13950977/80dd0174-f004-11e5-900a-255e97ec4da1.png)

As you may recall from the Overview lesson, the content for this default View,
'application', is determined by `app/application/template.hbs`. Let's look
inside that file for a moment.

```html
<h1> Welcome to Ember! </h1>

{{outlet}}
```

What exactly is `{{outlet}}`? We've talked about how Ember Views can be nested
within each other like a tree, just like the URLs that map to them -
`{{outlet}}` is a placeholder for any Views that might be nested inside the
'application' View.

Let's create one of those new Views and see how it works. Run the command `ember
g template about` in the console; it should create a new directory `app/about`,
with a file inside it called `template.hbs`. Let's write some new HTML into that
file:

```html
<h3> About This App </h3>

<p> This application is a demonstration of how Ember routing works. </p>
```

To let our application know what URL this View corresponds to, we need to add it
to the Ember Router.

```javascript
Router.map(function() {
  this.route('about');
});
```

Having made this change, if we navigate to `http://localhost:7165/about` we
should see the following.

!['about' View](https://cloud.githubusercontent.com/assets/388761/13951020/b5f539da-f004-11e5-8a1a-c08a2927036f.png)

WAIT!! Don't we need a Route, Controller, View, etc in order to be able to show
that 'about' View? Yes, we do! However, this is an example of Ember making
things simpler for us. By default, _defining a new route in the Router will
create all of the other objects for you_, and as a result, _the only time when
you actually create any of those objects (by making your own files) is when you
want to override the defaults_. It's amazing, and it lets you move very quickly
when developing an Ember application.

In this case, although we've only explicitly defined the Template, all of the
other objects have been created in the background. This is easily verified using
the Ember Inspector.

!['about' View, inspected](https://cloud.githubusercontent.com/assets/13922520/21613747/8404a0fa-d1a4-11e6-8ef0-00e03b5e01e8.png)

As you can see, even though we didn't create them, there exist a Route, a
Controller, and a View that are tied in to the template we've just created.

Let's go back to the main page - we can do this by using the "back" button.
Suppose that a user were to come to our site, and want to see the About page.
They could type in the URL manually, but that assumes that they know that the
`/about` route exists. It's also really bad UI. What would be nice is if we
could have a link pointing from our main page to our About page.

To that end, Ember provides us with a Handlebars helper called `{{#link-to}}`
that allows us to create links from one route to another.

`app/application/template.hbs`
```html
<nav>
  {{#link-to 'about'}}About{{/link-to}}
</nav>
```

This helper will generate an `<a>` tag inside our rendered HTML which loads the
About page.

While we're at it, let's add another link back to the main page, so we can get
back.

`app/application/template.hbs`
```diff
<nav>
+  {{#link-to 'application'}}Home{{/link-to}}
  {{#link-to 'about'}}About{{/link-to}}
</nav>
```

Now we can navigate back and forth between the two templates with ease!

## Lab: Routing to Templates

Create two more pages, 'Team' and 'Contact', that feature the following content:

### Team

```html
<h3> Our Team </h3>

<p> Our team is composed of the best folks around. </p>
```

### Contact

```html
<h3> Contact </h3>

<p>
  We can be found at: <br />
  1 Fake Street<br />
  NotRealsVille, MA, 00000
</p>
```

Set up routes that point to these templates, and add links to these pages from
the main page.

## Code-Along: Nested Templates

Suppose that we wanted to nest some more templates inside the Team page - for
instance, pages for Leadership, Engineering, and Sales - and have it be possible
to route to any of them.

When we wanted to route to 'about', 'team', and 'contact', we needed to have an
`{{outlet}}` in the 'application' Template; if we put another `{{outlet}}`
helper in the 'team' Template, we can load other, more deeply-nested templates
into that Template.

```html
<!-- team -->
<h3> Our Team </h3>

<p> Our team is composed of the best folks around. </p>

{{outlet}}
```

However, nested Templates require nested routes - URLs for these routes will
need to have the format `/team/engineering`, `/team/leadership`, etc. How can we
set this up?

If we go into the Ember Router, we can specify that routes are nested by passing
in a function as a second argument to `this.route` and defining new routes
within that function's body.

```javascript
Router.map(function() {
  this.route('about');
  this.route('contact');
  this.route('team', function(){
    this.route('engineering');
    this.route('leadership');
  });
});
```

Next, we need to set up our Templates. The appropriate syntax to use in the
generator is `ember g template team/something` - this will let ember-cli know to
create a `something` directory (containing a Template file) inside `app/team`,
much like how the `team` directory is nested within `app`.

Here's some content we can use for each of those nested templates.

The `{{#link-to}}` helpers referring back to the 'team' Template are not
strictly necessary, but are nice for UI reasons.

```html
<!-- team/leadership -->
<h5>Leadership Team</h5>
<ul>
  <li>Person One</li>
  <li>Person Two</li>
  <li>Person Three</li>
</ul>
{{#link-to 'team'}}Back{{/link-to}}
```

```html
<!-- team/engineering -->
<h5>Engineering Team</h5>
<ul>
  <li>Person Four</li>
  <li>Person Five</li>
  <li>Person Six</li>
</ul>
{{#link-to 'team'}}Back{{/link-to}}
```

Finally, let's add some `{{#link-to}}` helpers to the 'team' Template which
direct us to either team. With that done, we can easily navigate back and forth
between looking at either team. The way to refer to a nested route in 'string
form' is to separate each level of nesting with a `.`.

If you ever forget the route to a particular Template, you can always check the
Ember Inspector!

```html
<h3> Our Team </h3>

<p> Our team is composed of the best folks around. </p>

{{#link-to 'team.leadership'}}Leadership{{/link-to}}
{{#link-to 'team.engineering'}}Engineering{{/link-to}}

{{outlet}}
```

### Index Templates

Ordinarily, if we're looking at a page like 'team', the `{{outlet}}` helper
doesn't have any content in it. But what if we wanted to have something show up
there _only when no nested templates have been loaded_,  e.g. 'Click a team to
learn more.'?

This use case is handled by a special kind of nested Template called an 'index'.
Setting it up is almost the same as setting up any other kind of nested
Template, the only difference being that it maps to `…/` instead of
`…/somepath`.

We also don't need to modify the Router at all - it pre-defines an 'index' route
for every routing block (each corresponding Template should have an
`{{outlet}}`).

Let's create an index template for the 'team' page.

```js
ember g template team/index
```

The above will create `index/template.hbs` within the `team/` repository.

## Lab: Nested Templates

Change the 'contact' template to load one of two nested templates: 'boston' and
'nyc'. Each of these templates should have a different (fake) address visible.
When neither template is loaded, the 'contact' page should show the content
below:

```html
<p> Please select a location for contact details. </p>
```

## Additional Resources

-   [Ember Guides](http://guides.emberjs.com/v2.4.0/routing/)

## [License](LICENSE)

1.  All content is licensed under a CC­BY­NC­SA 4.0 license.
1.  All software code is licensed under GNU GPLv3. For commercial use or
    alternative licensing, please contact legal@ga.co.
