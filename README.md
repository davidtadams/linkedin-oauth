# OAuth and Passport JS

## Overview

- Read the objectives and discussion
- Scan the doc to see how it's organized
- Follow the instructions in Activities

## Objectives

There are two main goals for this lesson: to get familiar with Passport, and to hone your documentation interaction abilities.  Note "documentation interaction" - not "reading documentation".  Documentation is rarely a step-by-step tutorial.  Instead, it's an active process of knowing what to look for, seeking it out, pattern matching and only pulling in the few lines necessary to satisfy the errors you are getting.

By the end of this lesson, you should be able to:

- Describe and explain OAuth and it's role in web authentication (content)
- Carefully read error messages generated from framework code and reason through possible causes based on current knowledge of Express
- Use targeted searches in documentation to answer specific questions or inform hypothesis
  - use CMD+F on web pages
- Keep track of where you were on notecards, so you can maintain your place even when you have to solve smaller problems

You do _not_ need to memorize every step in this document.  Using framework code is all about combining what you know with your critical thinking skills and targeted documentation searches.  Most of this lesson is about non-cognitive behaviors.

## OAuth (content)

The basic OAuth2 web flow is:

![](http://41.media.tumblr.com/dc0ed4febc896d5d0589fc2940e52a42/tumblr_mp08klMuDm1qax653o1_1280.jpg)

Some guiding questions are:

- How does Google / Facebook / LinkedIn etc... communicate with your _local_ web app during development?  Isn't that private (aka not published on the internet)??
- What part of your existing authentication / authorization flows does this replace?
- Why would you want to authenticate with Google / Facebook instead of storing the emails / passwords yourself?

Resources:

- https://developer.linkedin.com/docs/oauth2
- https://github.com/auth0/passport-linkedin-oauth2
- http://passportjs.org/docs
- http://passportjs.org/docs/configure#configure
- https://apigee.com/console/linkedin
- http://docs.mongodb.org/manual/reference/method/db.collection.update/#db.collection.update

## Activities

## Clear the stage

**Clear the stage**: Close any terminal tabs, atom windows, chrome tabs etc... that you have open.  Just have this doc open to start.

**Know what and why**: Have you really read the objectives and discussion above?  There's a lot of copy-pasting happening today - it'll be easy to forget _why_ you are here.  Keep the big picture in mind as you develop.

**Value process over product**: While finishing the exercise is important, it's not a race.  Take your time - research things you have questions about, _really_ read all of the questions here and discuss them with classmates.  There's no prize for finishing first :)

## Get setup

Create a new, _public_ repo and push all of your changes there (don't fork and clone this repo)

## Boilerplate Part 1 - Create and deploy your app

Generate an express app that includes a `.gitignore` file:

```
express --git --hbs linkedin-oauth
cd linkedin-oauth
npm install
nodemon
```

Visit http://localhost:3000/ and make sure that the app loads correctly.  Then initialize a git repository:

```
git init .
git add -A
git commit -m "Initial commit"
```

Now create a repository on Github, set the remote properly and push.

## Boilerplate Part 2 - Deploy

Create an app on Heroku, deploy to it and verify that your app works on Heroku:

```
heroku apps:create
git push heroku master
heroku open
```

Now that you have a Heroku URL:

1. add a README file to your repo
1. add your Heroku URL to the README along with a brief description of the project
1. git add, commit and push to Github

## Install and configure passport w/ the LinkedIn strategy

Take a minute to read about Passport (http://passportjs.org/).  In particular, look through the Strategies to see all of the various web services you can connect to by default.  There are probably a number of web services you've never heard of - feel free to look them up.

We'll be using a LinkedIn strategy that isn't listed there.  The docs are here https://github.com/auth0/passport-linkedin-oauth2

In their [usage](https://github.com/auth0/passport-linkedin-oauth2#usage) section, they talk about how to configure the app.  It starts with this line:

```
var LinkedInStrategy = require('passport-linkedin-oauth2').Strategy;
```

Go ahead and put that line in `app.js` and run your app again.  What happens?

If you've just been following these instructions, it will error out.  The error states `Cannot find module 'passport-linkedin-oauth2'`.  What does that mean?

You haven't installed the package yet.  That's easy:

```
npm install passport-linkedin-oauth2 --save
```

Restart `nodemon` (it doesn't automatically detect changes to package.json) and reload your page in the browser.  So far so good...

Back in the [usage](https://github.com/auth0/passport-linkedin-oauth2#usage) section of the 'passport-linkedin-oauth2' docs, it says to add this to your app:

```js
passport.use(new LinkedInStrategy({
  clientID: LINKEDIN_KEY,
  clientSecret: LINKEDIN_SECRET,
  callbackURL: "http://127.0.0.1:3000/auth/linkedin/callback",
  scope: ['r_emailaddress', 'r_basicprofile'],
}, function(accessToken, refreshToken, profile, done) {
  // asynchronous verification, for effect...
  process.nextTick(function () {
    // To keep the example simple, the user's LinkedIn profile is returned to
    // represent the logged-in user. In a typical application, you would want
    // to associate the LinkedIn account with a user record in your database,
    // and return that user instead.
    return done(null, profile);
  });
}));
```

So go ahead and do that.  Where do you think it should go?  Put it above your routes, since that's where most middleware ends up.

Now if you refresh your page, BOOM!  Total blowup.  Look at the terminal - what error do you see?

`ReferenceError: passport is not defined` - hmmm.  That makes sense.  We haven't installed passport.

You are about to go to a different website to look at different docs.  So you don't forget what you are trying to do here, on a notecard write down "finish configuring passport-linkedin-oauth2 strategy".

Go to the passport docs and see how to install.  On the documentation page, you'll soon find

```
npm install passport
```

Is that line sufficient?  You should have picked up on the fact that you need the `--save` option, so instead of copy-pasting directly, you'll actually run:

```
npm install passport --save
```

Restart nodemon and see what happens.  `ReferenceError: passport is not defined` - you installed the package, but still need to do one more thing.  What is that?

```
var passport = require('passport')
```

Check the terminal again.  You've gotten a different error!

`ReferenceError: LINKEDIN_KEY is not defined`

OK, so we need some variable, `LINKEDIN_KEY`.  But what should we set it to?  Now it's time to go to LinkedIn and register an app.

Your app is broken right now, and you are about to go off on some other task.  Take a note card and write down "LINKEDIN_KEY is not defined" so you know where to come back to.

(NOTE: you should now have two things on your notecard)

## Register your LinkedIn Application

1. Login to https://www.linkedin.com/
1. Visit https://www.linkedin.com/developer/apps and create a new app
1. For Logo URL, add your own OR you can use https://brandfolder.com/galvanize/attachments/suxhof65/galvanize-galvanize-logo-g-only-logo.png?dl=true
1. Under website, add your Heroku URL
1. Fill in all other required fields and submit

On the "Authentication" screen:

- Under authorized redirect URLs enter http://localhost:3000/auth/linkedin/callback

You should see a Client ID and Client Secret.  

## Where were we?

Check your note card.  Oh - right, "LINKEDIN_KEY is not defined".

Go back to your app.  You see two lines like this:

```js
  clientID: LINKEDIN_KEY,
  clientSecret: LINKEDIN_SECRET,
```

LinkedIn just showed you two things, a Client ID and Client Secret.  Nice!  So now you should be able to get past that issue by replacing those placeholder variables with the values from LinkedIn:

```js
  clientID: 'xyzpdq',
  clientSecret: '18fy34g',
```

If you visit your homepge in the browser, you should see that the homepage works fine - no errors.  Sweet - cross that off your notecard.

What were you working on before that?  Oh, yeah - "finish configuring passport-linkedin-oauth2 strategy".

So go back to their docs.

## Setting up the auth route

You see the next example on https://github.com/auth0/passport-linkedin-oauth2#usage is:

```js
app.get('/auth/linkedin',
  passport.authenticate('linkedin', { state: 'SOME STATE'  }),
  function(req, res){
    // The request will be redirected to LinkedIn for authentication, so this
    // function will not be called.
  });
```

What does that look like?  It looks like a route.  But it starts with `app.get` - where should you put that?  Do some visual pattern recognition in your `app.js` and routes files, and you'll see that `app.js` references `app` a lot - so put it in there.  Probably underneath the other lines you've copied from the same docs.

Um... what should you do next?  Well, you just defined a route.  How would you check to see if that route is working?  Let's see what happens when we visit that route!

Visit [http://localhost:3000/auth/linkedin](http://localhost:3000/auth/linkedin)

Whoa.  "invalid redirect_uri. This value must match a URL registered with the API Key."  What's that about?  Let's take it apart:

The first part says "invalid redirect_uri".  Look around - where is it getting that word, "redirect_uri"?  Look in your terminal, in the URL, in the Chrome inspector.  It's gotta be somewhere.

You can see that it's in the URL.  Notice there's a query string key/value of `redirect_uri=http%3A%2F%2F127.0.0.1%3A3000`.  That's a URL-encoded value and it's hard to read.  What would it look like if it's _not_ URL-encoded?  Google "javascript url decode" and you'll see docs for things like `decodeURI` and `decodeURI` component.  OK - but how should you use them?  Just open up a `node` in Terminal, or open up a console in Chrome Web Developer Tools and type:

```
decodeURIComponent('redirect_uri=http%3A%2F%2F127.0.0.1%3A3000')
```

You'll see `redirect_uri=http://127.0.0.1:3000`.  OK - much more readable.  So where did `127.0.0.1:3000` come from?  We went to `localhost:3000` in our browser, right?

Search your codebase (in Atom it's CMD+Shift+F to find all in project).  You should come to this line:

```js
passport.use(new LinkedInStrategy({
  clientID: '783kvb1wqz3qbo',
  clientSecret: 'Lq9K44iUNXVHTiUX',
  callbackURL: "http://127.0.0.1:3000/auth/linkedin/callback",
  //...
```

We visited `localhost:3000`, but the callback URL is `127.0.0.1:3000`.  What did you register with LinkedIn? Go back to your app's page in the LinkedIn developer site and check.

You put `localhost:3000` there as well.  So let's update our LinkedInStrategy to match:

```js
passport.use(new LinkedInStrategy({
  clientID: '783kvb1wqz3qbo',
  clientSecret: 'Lq9K44iUNXVHTiUX',
  callbackURL: "http://localhost:3000/auth/linkedin/callback",
  //...
```

Where were we?  Oh right - visiting http://localhost:3000/auth/linkedin

Cool!  It's a new error.  This time it's saying it's a 404 to `/auth/linkedin/callback`.  Let's see what the docs have to say about that.

## Configuring the callback route

Over [here](https://github.com/auth0/passport-linkedin-oauth2#usage) notice that the docs show another example, like this:

```js
app.get('/auth/linkedin/callback', passport.authenticate('linkedin', {
  successRedirect: '/',
  failureRedirect: '/login'
}));
```

That looks like it'll do it.  Add that in.  Where should you add it?  Probably right below the other route you just added above.

How can you test it?  Visit [http://localhost:3000/auth/linkedin](http://localhost:3000/auth/linkedin) again.

Uh-oh.  What just happened?  Check the terminal:

`Error: passport.initialize() middleware not in use`

OK.  That seems like a passport error.  Let's go back to the passport docs.

You are about to switch contexts again, so maybe make a note like "get /auth/linkedin" so you know where to come back to.

## Configuring Passport

Passport docs, for a beginning express user, are pretty terrible.  Search the "Documentation" page for "express" and you'll eventually find:

```js
app.configure(function() {
  app.use(express.static('public'));
  app.use(express.cookieParser());
  app.use(express.bodyParser());
  app.use(express.session({ secret: 'keyboard cat' }));
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
});
```

That `app.configure` part doesn't look too familiar.  **BUT** the `app.use` lines do.  Compare the `app.use` lines from their docs to the `app.use` lines in your `app.js` - what are you missing?

We're trying to fix the `passport.initialize` error, so just add that one middleware:

```
app.use(passport.initialize());
```

What are you testing again?  Oh right, a GET to `/auth/linkedin` - run that again.  Now you get:

`Failed to serialize user into session`

Wat??  A little Googling might bring you [here](http://stackoverflow.com/questions/19948816/error-failed-to-serialize-user-into-session) where you see `passport.serializeUser` and `passport.deserializeUser`.

> Pro tip: Literally Google "Failed to serialize user into session" and the above link is the first result.

Those methods are being called on `passport`, so there's probably more information about that in the Passport docs.  Go there and search for it.  You'll find a snippet that looks like this in the passport docs:

```js
passport.serializeUser(function(user, done) {
  done(null, user.id);
});

passport.deserializeUser(function(id, done) {
  User.findById(id, function(err, user) {
    done(err, user);
  });
});
```

After some `console.log` statements and working through some errors (like not having a database or a `User` variable set etc...) you can arrive at something like this in `app.js`:

```js
// above app.use('/', routes);...
passport.serializeUser(function(user, done) {
  done(null, user);
});

passport.deserializeUser(function(user, done) {
  done(null, user)
});
```

We're still on "get /auth/linkedin" on the notecard - so check that again.  Did it work?  Are there any errors?

Notice in the log (in the terminal) you see `/auth/linkedin/callback?code=AQRbRXNzQ&state=SOME+STATE 302 917.981 ms` - nice!  That looks like a success.

It feels like time to commit, right?  Run a `git diff` to see all of your changes.  Scroll through with arrow keys and use `q` to quit.  What do you notice?  Anything there you don't want to commit?  Can you spot it?

Yup - you have a client id and secret right in your source code.  Before committing, it's important to get rid of that.

## Get dotenv setup

In case you forgot, here are the steps to getting dotenv setup:

```
npm install dotenv --save
touch .env
echo .env >> .gitignore
```

Take your client id and client secret and move them to your `.env` file:

```
LINKEDIN_CLIENT_ID=abc123
LINKEDIN_CLIENT_SECRET=abc123
```

Over in `app.js`, don't forget to require and load dotenv:

```js
require('dotenv').load()
```

And update your passport configuration to reference those variables:

```js
// below app.use(passport.session());...
passport.use(new LinkedInStrategy({
    clientID: process.env.LINKEDIN_CLIENT_ID,
    clientSecret: process.env.LINKEDIN_CLIENT_SECRET,
    callbackURL: "http://localhost:3000/auth/linkedin/callback",
    scope: ['r_emailaddress', 'r_basicprofile']
  },
  function(accessToken, refreshToken, profile, done) {
    done(null, {id: profile.id, displayName: profile.displayName, token: accessToken})
  }
));
```

Visit /auth/linkedin to make sure everything still works.

Phew!  NOW you are ready to commit.  It's been a while.  Add commit and push.

## We should probably deploy

```
git push heroku master
heroku open
```

Oh snap!  Application error.  How do you see what really happened? `heroku logs -t` should do it.  Make sure you see `TypeError: OAuth2Strategy requires a clientID option`.

Think through that.  You set the client id, right?  It looks like:

```js
clientID: process.env.LINKEDIN_CLIENT_ID
```

Ah - it's missing the environment variable.  You need to set those on Heroku:

```
heroku config:set LINKEDIN_CLIENT_ID=abc123 LINKEDIN_CLIENT_SECRET=def234
```

Visit `/auth/linkedin` on your heroku app.  You shouldn't see any errors in the log, and you should just see the homepage again.  Be super observant - watch the url.  We're going back to `localhost:3000`!!  Something is super wrong...

(Did you even notice that?  I mean, before we said that, did you notice the URL?  As a developer, make a habit of looking at the URL all the time, it's an important part of your work)

How did we tell LinkedIn where to go?  Up there in the callback url:

```js
callbackURL: "http://localhost:3000/auth/linkedin/callback",
```

We need to change this to work for both localhost and Heroku.  What's a good way to do that?

Environment variables well-suited to this.  Update your callback URL to look like this:

```js
callbackURL: process.env.HOST + "/auth/linkedin/callback",
```

Now make the necessary changes to .env and set the appropriate environment variable on Heroku.  You can figure this one out on your own :)

NOTE: the HOST variable should _not_ include a trailing slash.  So `https://secure-sands-4512.herokuapp.com` instead of `https://secure-sands-4512.herokuapp.com/`

If you need help, see [here](setting-host.md).

1. Test locally
1. Commit, push to github
1. Push to Heroku and test out /auth/linkedin - what do you see?

Darn.  `invalid redirect_uri. This value must match a URL registered with the API Key.` - remember how we debugged this before?  Look at the URL - the callback URL looks right this time.

So the problem must be `a URL registered with the API Key`.  Go to developer.linkedin.com and under your app's "Authorized Redirect URLs" enter your Heroku url, plus `/auth/linkedin/callback` and save.

Going to Heroku's `/auth/linked` route should now work!

That was intense.  Time for a break :)

## What's next?

Let's go back to https://github.com/auth0/passport-linkedin-oauth2 and see if we are done configuring.

It has a section about auto-handling the state param that includes a few small tweaks, like adding `state: true` in the config object, and removing the `state` property from the route.  Those look like good things to do.

## Configure the views

The app isn't particularly user-friendly yet.  If you know the route to login, it works, but now you'll want to:

- add login and logout links
- display the name of the currently logged-in user

To do so, add the following lines inside the `body` tag in the layout:

For jade:

```jade
    if user
      p
        |Hello #{user.displayName}
        ||
        a(href="/logout") Logout
    else
      p
        a(href="/auth/linkedin") Login
```

For hbs:

```jade
    {{#if user}}
      <p>
        Hello {{user.displayName}}
        |
        <a href="/logout">Logout</a>
      </p>
    {{else}}
      <p>
        <a href="/auth/linkedin">Login</a>
      </p>
    {{/if}}
```

Sweet - that seems to work.  Click "login" and it redirects to LinkedIn and then back.  But the username doesn't get populated.

Let's see if we can get that info into just the `/` route:

```js
router.get('/', function(req, res, next) {
  res.render('index', {
    title: 'Express',
    user: req.user
  });
});
```

Hmm... nothing yet.  But in a way that makes sense - we never set the user anywhere, right?  Let's check the docs:

http://passportjs.org/docs/authenticate

Nothing there that's too useful.  It says `req.user` should be there.  How have we done this before?  That is, how have we stored the user in the past?  Probably in the session.  If you go back to the docs and search for session, you'll come to this section: http://passportjs.org/docs/configure where you'll come back to this:

```js
app.configure(function() {
  app.use(express.static('public'));
  app.use(express.cookieParser());
  app.use(express.bodyParser());
  app.use(express.session({ secret: 'keyboard cat' }));
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
});
```

You saw that before - up above.  Notice that it installs some session middleware, and also `passport.session()`.  Looks like we'll have to do that.  Since we've already used `cookie-session`, install and configure that.  Be sure to store your secrets in `.env` and set those variables on Heroku when you deploy next.

Got `cookie-session` all wired up?  Is it working now?  Sadly no...

You are missing one small piece.  The object that comes back in profile is pretty large, and not in the format you want it.  You can `console.log` the `profile` to see this.  Instead, create a much smaller object that just has the info you need:

```js
passport.use(new LinkedInStrategy({
  clientID: process.env.LINKEDIN_CLIENT_ID,
  clientSecret: process.env.LINKEDIN_CLIENT_SECRET,
  callbackURL: process.env.HOST + "/auth/linkedin/callback",
  scope: ['r_emailaddress', 'r_basicprofile'],
  state: true
}, function(accessToken, refreshToken, profile, done) {
  done(null, {id: profile.id, displayName: profile.displayName})
}));
```

Now when you login, you should actually see your name appear on the index page.

Nice!  Seems like a good time to commit.  Be sure your cookie session keys are not checked in.

## Get the name on every page

Now add some middleware that will set the `user` local variable in all views:

```js
// right above app.use('/', routes);
app.use(function (req, res, next) {
  res.locals.user = req.user
  next()
})
```

Now your `index.js` route can simply go back to:

```js
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});
```

This was a simple refactor, and should still work both locally and on Heroku.  Verify, then commit and push.

Questions:

- What is `res.locals`?  And where can you go to find a definitive answer to that question?
- Where does `req.user` come from?  You never set that, right?

## What about logout?

You should now be able to login with LinkedIn, but if you click the "logout" button, it shows a 404.

This is totally normal express now - just add a `/logout` route that clears the session!

When you are done:

1. Git add, commit and push
1. Deploy to Heroku
1. Check that your app works on Heroku

## Make an API call to LinkedIn on the user's behalf

Passport handles all the details of communicating with an external web service and at the end it provides you with a single auth token (for OAuth 2).  You can use that auth token to make API calls _on behalf of the user_.  This is incredibly powerful, since your website can now "pretend" that it's a logged in user on LinkedIn, or Twitter etc...

In order to make simple web requests, install unirest:

```
npm install unirest --save
```

Update your `routes/index.js` file to this:

```js
var express = require('express');
var router = express.Router();
var unirest = require('unirest');

/* GET home page. */
router.get('/', function(req, res, next) {
  if(req.isAuthenticated()) {
    unirest.get('https://api.linkedin.com/v1/people/~:(id,num-connections,picture-url)')
      .header('Authorization', 'Bearer ' + req.user.token)
      .header('x-li-format', 'json')
      .end(function (response) {
        console.log(response);
        res.render('index', { profile: response.body });
      })
  } else {
    res.render('index', {  });
  }
});

module.exports = router;
```

Update your `views/index` template to this:

jade:
```
extends layout

block content
  if profile
    img(src=profile.pictureUrl)
```

hbs:
```
  {{#if profile}}
    <img src="{{profile.pictureUrl}}">
  {{/if}}
```

In your browser, you _should_ now see your LinkedIn profile picture appear (if you have one).  But you don't.  Check out your console - that response coming back from LinkedIn doesn't look good at all.  It shows (deeply nested in there):

`Invalid access token.`

Go find that error message and think about what it means for a minute.

This last problem you have to solve on your own.  Here are some hints:

- Look through all the code you added for anything that looks like it could be a valid access token
- Draw out the order in which `passport.use`, the routes, the `serializeUser` etc... happen.  Use `console.log` to prove that the order of events you know of is correct
- Think back to previous steps - what was involved in setting `req.user`?  Where did that data object originally come from?

If you are super stuck after, say, an hour of calmly and methodically going through this process, see the answer [here](setting-access-token.md).

You know you are successful when your LinkedIn picture appears on the screen.

---

# See how that works?

Name 4 places where you didn't copy/paste from documentation directly.  Notice how rarely you can just copy/paste from documentation and have it *just work*.

# Reflect: Self-asses

- Check the objectives above.  Have you met each objective?  What evidence do you have of that?
- Read through the questions at the top and answer them.  Do you feel confident in your answers?
  - if not, take some time to research

# Reflect: New Questions

What new questions do you have, now that you've gone through this exercise:

1.
1.
1.
1.

---

# Additional features

## Stretch Option: Save profiles to your Mongo database

In a real-world application, it's common to take information from external services and store that info in your own database.  Add code so that whenever a user logs in, it saves their data in a document in your users collection.  To begin:

1. Install monk
1. Configure monk to use an environment variable for its connection string (MONGOLAB_URI)
1. Add Mongolab to Heroku (`heroku addons:create mongolab`)

Then you'll need to find-and-update or insert a document every time someone logs in.  Mongo has an [`upsert` option](http://docs.mongodb.org/manual/reference/method/db.collection.update/#db.collection.update) in its `update` method that's perfect for this.

Here's a decent place to add this mongo code (see comments inline):

```js
passport.use(new LinkedInStrategy({
    clientID: process.env.LINKEDIN_CLIENT_ID,
    clientSecret: process.env.LINKEDIN_CLIENT_SECRET,
    callbackURL: process.env.HOST + "/auth/linkedin/callback",
    scope: ['r_emailaddress', 'r_basicprofile'],
    state: true
  },
  function(accessToken, refreshToken, profile, done) {
    // here, find or create a document in the user collection, and update it's contents
    // NOTE: the profile object is unnecessarily big.  Only store the parts you care about here.
    done(null, {id: profile.id, displayName: profile.displayName, token: accessToken})
  }
));
```

## Stretch Option: Post updates to LinkedIn

Add functionality to post an update to LinkedIn.  CAREFUL!  It will actually update your real LinkedIn feed :)

## Stretch Option: Only store the user id in the session

This tutorial has you store the user's LinkedIn id, displayName and accessToken in the session.

Instead, store all that info on the users document, and then only store the user's `_id` in the session.

Now you have a record of everyone who logged in, and you can easily expand your site to accomodate multiple other providers.

## Stretch Option: Add new strategies

Allow users to also login with Twitter, or GitHub, or any strategy listed on http://passportjs.org/
