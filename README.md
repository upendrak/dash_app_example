# Deploying Dash App in Heroku

To share a Dash app to the outside world, you need to "deploy" your Dash app to a server and open up the server's firewall to the public or to a restricted set of IP addresses. Dash uses Flask under the hood. This makes deployment easy: you can deploy a Dash app just like you would deploy a Flask app. Almost every cloud server provider has a guide for deploying Flask apps.

Heroku is one of the easiest platforms for deploying and managing public Flask applications.

[View the official Heroku guide to Python.](https://devcenter.heroku.com/articles/getting-started-with-python#introduction)

Here is a simple example. This example requires a Heroku account, git, and virtualenv.

**Step 1.** Create a new folder for your project:

```
$ mkdir dash_app_example
$ cd dash_app_example
```

**Step 2.** Initialize the folder with git and a virtualenv

```
$ git init        # initializes an empty git repo
$ virtualenv venv # creates a virtualenv called "venv"
$ source venv/bin/activate # uses the virtualenv
```

`virtualenv` creates a fresh Python instance. You will need to reinstall your app's dependencies with this virtualenv:

```
$ pip install dash
$ pip install plotly
```

You will also need a new dependency, `gunicorn`, for deploying the app:

```
$ pip install gunicorn
```

**Step 3.** Initialize the folder with a sample app (`app.py`), a `.gitignore` file, `requirements.txt`, and a `Procfile` for deployment

Create the following files in your project folder:

```
$ vi app.py

import os

import dash
import dash_core_components as dcc
import dash_html_components as html

external_stylesheets = ['https://codepen.io/chriddyp/pen/bWLwgP.css']

app = dash.Dash(__name__, external_stylesheets=external_stylesheets)

server = app.server

app.layout = html.Div([
    html.H2('Hello World'),
    dcc.Dropdown(
        id='dropdown',
        options=[{'label': i, 'value': i} for i in ['LA', 'NYC', 'MTL']],
        value='LA'
    ),
    html.Div(id='display-value')
])

@app.callback(dash.dependencies.Output('display-value', 'children'),
              [dash.dependencies.Input('dropdown', 'value')])
def display_value(value):
    return 'You have selected "{}"'.format(value)

if __name__ == '__main__':
    app.run_server(debug=True)
```

```
vi .gitignore

venv
*.pyc
.DS_Store
.env
```

```
vi Procfile

web: gunicorn app:server
```

> Note that **app** refers to the filename `app.py`. **server** refers to the variable server inside that file.

`requirements.txt` describes your Python dependencies. You can fill this file in automatically with:

```
$ pip freeze > requirements.txt
```

**Step 4.** Initialize Heroku, add files to Git, and deploy

```
$ heroku create dash-app-demo-example # change my-dash-app to a unique name
Creating ⬢ dash-app-demo-example... done
https://dash-app-demo-example.herokuapp.com/ | https://git.heroku.com/dash-app-demo-example.git 
```

```
$ git add . # add all files to git
$ git commit -m 'Initial app boilerplate'
$ git push heroku master # deploy code to heroku
```

```
$ heroku ps:scale web=1  # run the app with a 1 heroku "dyno"
```

You should be able to view your app at https://dash-app-demo-example.herokuapp.com/.

**Step 5.** Update the code and redeploy

When you modify `app.py` with your own code, you will need to add the changes to git and push those changes to heroku.

```
$ git status # view the changes
$ git add .  # add all the changes
$ git commit -m 'a description of the changes'
$ git push heroku master
```

**Step 6.** Push to your own github repo

If you want to push the changes to your own github repo, then navigate to github.com, create a repository and add the remote to the existing local repo
