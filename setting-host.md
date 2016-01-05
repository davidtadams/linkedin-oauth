## Set the HOST environment variable

```
echo HOST=http://localhost:3000 >> .env
```

Then set the HOST environment variable on Heroku to your Heroku URL:

```
heroku config:set HOST=https://your-heroku-app.herokuapp.com
```

NOTE: do _not_ include the trailing slash.  So `https://guarded-inlet-5817.herokuapp.com` instead of `https://guarded-inlet-5817.herokuapp.com/`
