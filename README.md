 # Tensor Bridge on Heroku
 
Deploy both [tensor-bridge](https://github.com/Babylonpartners/tf-bridge) and [tensorflow-serving](https://www.tensorflow.org/serving/) so that you can use JSON to talk to your TensorFlow models.

## Deploy

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/heroku/tf-bridge/tree/heroku-deploy)

### From Source

```
git clone git@github.com:heroku/tf-bridge.git
cd tf-bridge
heroku create <your-appname>
heroku buildpacks:add -i 1 https://github.com/heroku/heroku-buildpack-apt.git
heroku buildpacks:add -i 2 https://github.com/heroku/heroku-buildpack-python.git
heroku buildpacks:add -i 3 https://github.com/danp/heroku-buildpack-runit.git
heroku config:set TENSORFLOW_MODEL_URL=https://s3.amazonaws.com/<your-public-bucket>/<your-publicly-accessible-model>.tar.gz
git push heroku heroku-deploy:master
```

## Test the Server

A pre-made publicly available model is provided here:

```
https://s3.amazonaws.com/octo-public/wide_deep_model.tar.gz
```
More information about this model can be found [here](https://www.tensorflow.org/tutorials/wide_and_deep).

You can set the model's URL as a config var via the button deploy or set it manually: 

```
heroku config:set TENSORFLOW_MODEL_URL=https://s3.amazonaws.com/octo-public/wide_deep_model.tar.gz -a <your_app_name>
```

A client to test the provided model has also been provided as a sub project in this repo. To run the client and test your server:

```
cd wide-deep
pipenv --three
pipenv install
pipenv run python wide_deep_client.py <your-appname>.herokuapp.com:80
```

If all goes well you should see:

```
--------------------------
--------------------------
Accuracy:  0.8
--------------------------
--------------------------
```

## Notes
1. The [Apt buildpack](https://github.com/heroku/heroku-buildpack-apt) loads `tensorflow-model-server` & deps
1. The [Runit buildpack](https://github.com/danp/heroku-buildpack-runit) manages `tensorflow-serving` & `tf-bridge` processes
1. `.profile.d` script loads models from the `TENSORFLOW_MODEL_URL` config var
1. Models must be exported using the `SavedModelBuilder` module, which is outlined [here](https://www.tensorflow.org/serving/serving_basic)

