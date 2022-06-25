# event-registration-app-server
Event Registration Application: Server part.
This is Server part for Client part of the repository
https://github.com/vasilyyaremchuk/event-registration-app-client

# Installation

In this guide described installation with Lando local development tool, but you can set up it on other dev tool or hosting by the similar steps.
See more on https://lando.dev/

1. Create file 'default.env' with your Amazon AWS keys near '.lando.yml':
```
AWS_ACCESS_KEY_ID=your_Access_Key_ID
AWS_SECRET_ACCESS_KEY=your_Secret_Access_Key
```

2. Start Lando:
```$ lando start```

3. Install dependencies with composer:
```$ lando composer install```

4. Copy custom folder in 'web/modules':
```$ cp -r custom web/modules```

5. Import DB dump:
```$ lando db-import dump.sql.gz```

6. We are going to use Drupal as Backend with Json API, so we need to take care about cors polices.

In /web/sites/default/ folder find file 'default.services.yml' and make a copy with name 'services.yml'. In the end of that new file find cors.config and align settings:
```
  cors.config:
    enabled: true
    # Specify allowed headers, like 'x-allowed-header'.
    allowedHeaders: ['x-csrf-token','authorization','content-type','accept','origin','x-requested-with', 'access-control-allow-origin','x-allowed-header']
    # Specify allowed request methods, specify ['*'] to allow all possible ones.
    allowedMethods: ['POST', 'GET', 'OPTIONS', 'PATCH', 'DELETE']
    # Configure requests allowed from specific origins.
    allowedOrigins: ['*']
    # Sets the Access-Control-Expose-Headers header.
    exposedHeaders: true
    # Sets the Access-Control-Max-Age header.
    maxAge: false
    # Sets the Access-Control-Allow-Credentials header.
    supportsCredentials: true
```

7. You have to run install
https://event-server.lndo.site/core/install.php

select 'Minimal' profile,

specify access to Lando DB:

database name: drupal9
database user: drupal9
database password: drupal9
host: database

You have to get message 'Drupal already installed' in the end.

Alternativly, you can just copy 'settings.php' from the root to /web/sites/default/ folder.
```$ cp settings.php web/sites/default```
and create in web/sites/default/ also /files/ folder

8. You can get one time login link:
```$ lando drush uli```

Be sure to replace 'http://default' by your local site address.

9. Setup password for your admin user there
/user/2/edit?destination=/admin/people

10. Create 'keys' folder near '.lando.yml' file. Generate Keys for simple OAuth setting on /admin/config/people/simple_oauth
Click "Generate keys" button. Input directory '../keys'. Save configuration.

11. On page '/admin/config/services/consumer/1/edit' setup user and secret that you will use in you client application setup.

## Possible issue and improvments

1. The permissions set need to be set more accurate on '/admin/people/permissions'.
For example, '/admin/content/participant/add/event_participant'
available for anonymous. We need to add captcha at least or think some additional role that can sens participants from client application.

2. Bulk export of contacts in CSV now is publically available, we need to add authorisation.
See '/admin/structure/views/view/participants'

3. In the custom 'participant_sqs' module we use hook_entity_insert that isn't best practice, but we did that in the metter of simplisity. The better will be use Hook Event Dispatcher module that based on the Drupal Event Subscriber system.

4. The address where the information for new participant goes hardcoded in lambda function (https://github.com/vasilyyaremchuk/event-registration-app-lambda), but it worth to be managebal in the Drupal admin.

## References

https://github.com/DrupalizeMe/react-and-drupal-examples

https://www.drupal.org/docs/core-modules-and-themes/core-modules/jsonapi-module/creating-new-resources-post

https://medium.com/thefirstcode/cors-cross-origin-resource-sharing-in-drupal-8-19778cf2838a

https://www.drupal.org/project/hook_event_dispatcher

https://gist.github.com/fbrnc/396548c85ee083e32930

https://github.com/vasilyyaremchuk/event-registration-app-lambda

https://github.com/vasilyyaremchuk/event-registration-app-client
