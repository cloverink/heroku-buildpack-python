![python](https://cloud.githubusercontent.com/assets/51578/13712821/b68a42ce-e793-11e5-96b0-d8eb978137ba.png)

# Heroku Buildpack: Python

[![Build Status](https://travis-ci.org/heroku/heroku-buildpack-python.svg?branch=master)](https://travis-ci.org/heroku/heroku-buildpack-python)

This is the official [Heroku buildpack](https://devcenter.heroku.com/articles/buildpacks) for Python apps, powered by [Pipenv](http://docs.pipenv.org/), [pip](https://pip.pypa.io/) and other excellent software.

Recommended web frameworks include **Django** and **Flask**. The recommended webserver is **Gunicorn**. There are no restrictions around what software can be used (as long as it's pip-installable). Web processes must bind to `$PORT`, and only the HTTP protocol is permitted for incoming connections.

Python packages with C dependencies that are not [available on the stack image](https://devcenter.heroku.com/articles/stack-packages) are generally not supported, unless `manylinux` wheels are provided by the package maintainers (common). For recommended solutions, check out [this article](https://devcenter.heroku.com/articles/python-c-deps) for more information. 

See it in Action
----------------

Deploying a Python application couldn't be easier:

    $ ls
    Pipfile		Pipfile.lock	Procfile	web.py

    $ heroku create --buildpack heroku/python

    $ git push heroku master
    …
    -----> Python app detected
    -----> Installing python-3.6.6
    -----> Installing pip
    -----> Installing requirements with Pipenv 2018.5.18…
           ...
           Installing dependencies from Pipfile…
    -----> Discovering process types
           Procfile declares types -> (none)

A `Pipfile` or `requirements.txt` must be present at the root of your application's repository.

You can also specify the latest production release of this buildpack for upcoming builds of an existing application:

    $ heroku buildpacks:set heroku/python


Specify a Python Runtime
------------------------

Specific versions of the Python runtime can be specified in your `Pipfile`:

    [requires]
    python_version = "2.7"

Or, more specifically:

    [requires]
    python_full_version = "2.7.15"

Or, with a `runtime.txt` file:

    $ cat runtime.txt
    python-2.7.15

Runtime options include:

- `python-3.7.0`
- `python-3.6.6`
- `python-2.7.15`


Specify an SSH Key
------------------

If you need to install dependencies stored in private repositories, but you don't want to hardcode passwords in the code,
you can use the following approach.


- Generate or use an existing a new SSH key pair (https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)

  For this example, assume that you named the key `deploy_key`.

- Add the public ssh key to your private repository account.

  * Github: https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/

  * Bitbucket: https://confluence.atlassian.com/bitbucket/add-an-ssh-key-to-an-account-302811853.html

- Add CUSTOM_SSH_KEY and CUSTOM_SSH_KEY_HOSTS environment variables to you heroku app

  * CUSTOM_SSH_KEY must be base64 encoded
  * CUSTOM_SSH_KEY_HOSTS is a comma separated list of the hosts that will use the custom SSH key

  ```
  # OSX
  $ heroku config:set CUSTOM_SSH_KEY=$(base64 --input ~/.ssh/deploy_key.pub) CUSTOM_SSH_KEY_HOSTS=bitbucket.org,github.com

  # Linux
  $ heroku config:set CUSTOM_SSH_KEY=$(base64 ~/.ssh/deploy_key.pub | tr -d '\n') CUSTOM_SSH_KEY_HOSTS=bitbucket.org,github.com
  ```

- Deploy your app and enjoy!
