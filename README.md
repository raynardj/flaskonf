# Flaskonf
> Configuration Driven API


## Installation

Under your python environment

```shell
pip install flaskonf
```

## Create API with configuration json files

Suppose you have configuration files like following

```python
!ls ../tests/confs/
```

    la.json       shanghai.json


```python
!cat ../tests/confs/*.json
```

    {
        "name":"Los Angeles",
        "country":"United States",
        "sites":["universal studio", "griffith observatory", "union station"]
    }
    {
        "name":"Shanghai",
        "country":"China",
        "sites":["french concession", "xin tiandi", "disney resort"]
    }


```python
from flaskonf.api import FlaskonfAPI
from typing import Dict
```

```python
app = FlaskonfAPI("AnConfAPI")
```

Bind the app to the configuration directory

Example directory is for test inputs example, you can just leave it empety for now

```python
app.build_on_config(
    confs_dir="../tests/confs/",
    examples_dir="../tests/examples/" )
```

### Create API blueprint

The syntax looks like creating API, but this is a blueprint based on what we will create an **API for each json file**

```python
@app.conf_route("/guide/", nobuild=True)
def guide_api(inputs: Dict):
    data = inputs['data']
    conf = data["conf"]
    user = data["user"]
    return {"city_data": conf, "user": user}

app.build_flaskonf()
```

Notice, the configuration data is accessible in the decorated function

The rest is flask usual you are familiared with, if you run like following, you can visit ```localhost:6060/guide/la/``` or ```localhost:6060/guide/shanghai/``` to test your API

```python
app.run("0.0.0.0", port = 6060)
```

     * Serving Flask app "AnConfAPI" (lazy loading)
     * Environment: production
       WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Debug mode: off


     * Running on http://0.0.0.0:6060/ (Press CTRL+C to quit)
    INFO:werkzeug: * Running on http://0.0.0.0:6060/ (Press CTRL+C to quit)


## When loading API components takes effort
> eg. when you have to load huge AI model into memory when starting the server

Set ```nobuild=True```

```python
@app.conf_route("/guide2/", nobuild=False)
def build_city_guide(conf_file: str, conf: Dict):
    logging.info(f"{conf}")
    # doing other things for building API here
    # like load huge model into memory with configuration
    def guide_api(inputs: Dict):
        data = inputs['data']
        user = data["user"]
        return {"city_data": conf, "user": user}
    return guide_api
```
