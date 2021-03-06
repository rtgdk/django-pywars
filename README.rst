=====
Pywars
=====

Pywars is a Turn based Tank battle game for coding challenge.

Detailed documentation is in the "docs" directory.

Quick start
-----------

1. Add "pywars" to your INSTALLED_APPS setting like this::

    INSTALLED_APPS = (
        ...
        'pywars',
    )

2. Include the pywars URLconf in your project urls.py like this::

    url(r'^pywars/', include('pywars.urls')),

3. Run `python manage.py migrate` to create the pywars models.

4. Start the development server and visit http://127.0.0.1:8000/admin/

5. Visit http://127.0.0.1:8000/pywars/ to open the app.



## Table of contents ##
- [Introduction](#introduction)
- [System configuration](#system-configurations)
- [Game rules](#game-rules)
    - [Programming the tank bot](#programming-the-tank-bot)
- [Running matchs](#running-the-celery-workers-for-running-matches)

## Introduction ##

A turn based tank game for Python bots

## System configuration ##
This is the configuration for development. Make sure you have a virtualenv. You need to do the following:
* Install the dependencies running `pip install -r requirements.txt`
* Create the database running `python manage.py migrate`
* Create an admin user with `python manage.py createsuperuser`
* We need Bower intalled, if we have it we can run this:  `python manage.py bower install`
* Run the development server `python manage.py runserver`
* Open the browser at `http://127.0.0.1:8000/`

##Intall Bower##
* Fedora

    ```bash
    sudo yum install npm
    sudo npm install -g bower
    ```

* Ubuntu

    ```bash
    sudo apt-get install npm
    sudo npm install -g bower
    ```

In Ubuntu 14.04 we need do this


    sudo ln -s /usr/bin/nodejs /usr/bin/node


## Game rules ##
The game is simple, you will program a tank-bot that should destroy the oponent's tank. The game is by turns, and in each turn the tank has three possible actions to take:
  * **Shoot**: The tank can fire a projectile against its enemy target. That will cause two possible results:
    - The projectile hits the enemy, reducing its life by a certain amount.
    - The projectile misses it and nothing happens.
  * **Move**: The tank can move forward or back, but only one step per turn.
  * **Do Nothing**: Pretty self explanatory. Nothing happens here.

The game ends when one of the tanks is destroyed, i.e: its life reaches 0 points; or when 100 turns are played, in which case the winning tank is the one which has more lives at that moment.

### Programming the tank bot ###
The user must define a class called `Bot` that implements the method `evaluate_turn`, which receives the following parameters:
* `feedback`: The result of the previous turn. For instance, the shoot action 'SUCESS' is returned if the enemy is hit, and 'FAILED' otherwise.
* `life`: Current tank life. A number between 0-100.

Taking this game status as an input, the method should evaluate programmatically the best strategy for the tank; and then return a dictionary indicating what the tank will do in its turn. For this, the player has a few options:
  * **Move**: Return a dictionary like this `{'ACTION': 'MOVE', 'WHERE': 1}`, where 1 is for moving forward and -1 is for moving backwards.
  * **Shoot**: Return a dictionary like this `{'ACTION': 'SHOOT', 'ANGLE': 35, 'VEL': 100}` where _ANGLE_ values must be between _0_ and _89_ and _VEL_ must be between _0_ and _100_.
  * **Stay**: Player decides not doing anything, so it returns an empty dictionary.

## IMPORTANT ##
Code used to implement `evaluate_turn` must run in **PyPy**. For security reasons, this code runs in a PyPy Sandbox, so keep in mind that modules like `random` or `time` are not available, so you must figure out how to live with that :P

# Running the Celery worker for running matches:

    celery -A battleground worker -l info