# Instructions

The docker files are derived from and have several modifications for bugs ir further automation: https://raw.githubusercontent.com/tensorflow/serving/master/tensorflow_serving/tools/docker/Dockerfile.devel

- Build the images and push them:

`build.sh --docker-username  xxxxx -docker-password xxxxx`

 Build only locally:

`build.sh --build --docker-username  xxxxx`

to build locally the two images for tensorflow serving. One for cpu support and the other
for gpu as well.

TODO: migrate images from 14.04 to 16.04.

- Run the tensorflow server with the example here (https://tensorflow.github.io/serving/serving_advanced):

`docker run -it --name tensorflow-serving-cpu -p 9000:9000 skonto/tensorflow-serving-cpu:latest bash -c "cd /serving && bazel build //tensorflow_serving/example:mnist_saved_model && bazel-bin/tensorflow_serving/example/mnist_saved_model --training_iteration=100 --model_version=1 /tmp/mnist_model && bazel build //tensorflow_serving/example:mnist_client && bazel-bin/tensorflow_serving/model_servers/tensorflow_model_server --enable_batching --port=9000 --model_name=mnist  --model_base_path=/tmp/mnist_model/"`

`docker run -it --device /dev/nvidia0:/dev/nvidia0 --device /dev/nvidiactl:/dev/nvidiactl --device /dev/nvidia-uvm:/dev/nvidia-uvm  --name tensorflow-serving-gpu -p 9000:9000 skonto/tensorflow-serving-cpu:latest bash -c "cd /serving && bazel build //tensorflow_serving/example:mnist_saved_model && bazel-bin/tensorflow_serving/example/mnist_saved_model --training_iteration=100 --model_version=1 /tmp/mnist_model && bazel build //tensorflow_serving/example:mnist_client && bazel-bin/tensorflow_serving/model_servers/tensorflow_model_server --enable_batching --port=9000 --model_name=mnist  --model_base_path=/tmp/mnist_model/"`

Run the client inside the docker:
`docker exec -it skonto/tensorflow-serving-cpu:latest /serving/bazel-bin/tensorflow_serving/example/mnist_client --num_tests=1000 --server=localhost:9000 --concurrency=10`

Run the tests:

`docker exec -it skonto/tensorflow-serving-cpu:latest cd /serving && bazel test tensorflow_serving/...`

Issues found so far:

- https://github.com/tensorflow/serving/issues/318

- https://github.com/tensorflow/serving/issues/295

- https://github.com/tensorflow/serving/issues/380
(no dynamic support for model update should be doable with code modifications)

- https://github.com/tensorflow/serving/issues/327
