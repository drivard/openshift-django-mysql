Django on OpenShift Express
============================

This git repository helps you get up and running quickly w/ a Django installation
on OpenShift Express.  The Django project name used in this repo is 'openshift'
but you can feel free to change it.  <strike>Right now the backend is sqlite3 and the
database runtime is @ $OPENSHIFT_DATA_DIR/sqlite3.db.</strike>Right now the backend is MySQL.

When you push this application up for the first time, the sqlite database is
copied from wsgi/openshift/sqlite3.db.  This is the stock database that is created
when 'python manage.py syncdb' is run with only the admin app installed.

You can delete the database from your git repo after the first push (you probably
should for security).  On subsequent pushes, a 'python manage.py syncdb' is
executed to make sure that any models you added are created in the DB.  If you
do anything that requires an alter table, you could add the alter statements
in GIT_ROOT/.openshift/action_hooks/alter.sql and then use
GIT_ROOT/.openshift/action_hooks/deploy to execute that script (make sure to
back up your database w/ 'rhc app snapshot save' first :) )


Running on OpenShift
----------------------------

Create an account at http://openshift.redhat.com/

Create a python-2.6 application

    rhc app create -a django -t python-2.6

Add MySQL Database service

    rhc-ctl-app -a django -e add-mysql-5.1

Add PHPMyAdmin service

    rhc-ctl-app -a django -e add-phpmyadmin-3.4

Add this upstream seambooking repo

    cd django
    <strike>git remote add upstream -m master git://github.com/openshift/django-example.git</strike>
    git remote add upstream -m master git://github.com/drivard/django-example.git
    git pull -s recursive -X theirs upstream master
    
Then push the repo upstream

    git push

Create the Django admin user

Find your openshift app UID

    rhc domain

Connect through ssh to your app

    ssh $app_uid@django-$yournamespace.rhcloud.com

Get in the app directory

    cd django/repo/wsgi/openshift

Export the python egg cache directory

    export PYTHON_EGG_CACHE=$OPENSHIFT_APP_DIR/virtenv/lib/python-2.6

Create the admin user

    ../../../../virtenv/bin/python ./manage.py createsuperuser

That's it, you can now checkout your application at (Using the user you just created.):

    http://django-$yournamespace.rhcloud.com

