# Heroku Wildfly Buildpack

This is a [Heroku Buildpack](https://devcenter.heroku.com/articles/buildpacks) for running [Wildfly AS](http://wildfly.org) attached to a PostgreSQL DB.

## Usage

Put your WAR file(s) in `target/` and deploy.

## Using with the Java buildpack

You can use the standard Heroku Java buildpack to compile your WAR file, and then have Wildfly run it:

```sh-session
$ heroku buildpacks:clear
$ heroku buildpacks:add heroku/java
$ heroku buildpacks:add https://github.com/uareurapid/heroku-buildpack-wildfly
```

Then deploy your Maven project with a `pom.xml`, Heroku will run the Java buildpack to compile it, and as long as you output a `target/*.war` file the Wildfly buildpack will run it.

This buildpack will download wildfly 10.1.0.Final, install the PostgreSQL driver and setup SMTP via a Gmail account.

Regarding the Gmail configuration, an additional file named "replace_email_config.sh" should be placed on the root folder of your repo.
its contents should be more or less like this:

#!/usr/bin/env bash

#get the template path
STANDALONE_XML=$1
GMAIL_USERNAME=$2
GMAIL_PASSWORD=$3
#replace config for sending emails (via gmail account) 
sed -i -e 's|<smtp-server outbound-socket-binding-ref="mail-smtp"/>|<smtp-server outbound-socket-binding-ref="mail-smtp1"/></mail-session><mail-session name="Gmail" debug="true" jndi-name="java:jboss/mail/gmail"><smtp-server outbound-socket-binding-ref="mail-smtp" ssl="true" username="'$GMAIL_USERNAME'" password="'$GMAIL_PASSWORD'"/>|' $STANDALONE_XML

sed -i -e 's|<remote-destination host="localhost" port="25"/>|<remote-destination host="smtp.gmail.com" port="465"/>|' $STANDALONE_XML

where GMAIL_USERNAME and GMAIL_PASSWORD should be config vars defined on your heroku app dashboard


