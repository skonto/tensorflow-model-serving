# Instructions

The docker files are derived from the link bellow and they have several modifications for bugs for further automation: https://raw.githubusercontent.com/tensorflow/serving/master/tensorflow_serving/tools/docker/Dockerfile.devel

- Build the images and push them:

`build.sh --docker-username  xxxxx -docker-password xxxxx`

 Build only locally:

`build.sh --build --docker-username  xxxxx`

to build locally the two images for tensorflow serving. One for cpu support and the other
for gpu as well.

TODO: migrate images from 14.04 to 16.04.

## Run the tensorflow server with the example [here](https://tensorflow.github.io/serving/serving_advanced)

**CPU case** :

`docker run -it --name tensorflow-serving-cpu -p 9000:9000 skonto/tensorflow-serving-cpu:latest bash -c "cd /serving  && bazel-bin/tensorflow_serving/example/mnist_saved_model --training_iteration=100 --model_version=1 /tmp/mnist_model &&  bazel-bin/tensorflow_serving/model_servers/tensorflow_model_server --enable_batching --port=9000 --model_name=mnist  --model_base_path=/tmp/mnist_model/"`

Run the client inside the docker

`docker exec -it tensorflow-serving-cpu /serving/bazel-bin/tensorflow_serving/example/mnist_client --num_tests=1000 --server=localhost:9000 --concurrency=10`

Run the tests:

`docker exec -it tensorflow-serving-cpu cd /serving && bazel test tensorflow_serving/...`


**GPU case** :

`docker run -it --device /dev/nvidia0:/dev/nvidia0 --device /dev/nvidiactl:/dev/nvidiactl --device /dev/nvidia-uvm:/dev/nvidia-uvm  --name tensorflow-serving-gpu -p 9000:9000 skonto/tensorflow-serving-gpu:latest bash -c "cd /serving && bazel-bin/tensorflow_serving/example/mnist_saved_model --training_iteration=100 --model_version=1 /tmp/mnist_model && bazel-bin/tensorflow_serving/model_servers/tensorflow_model_server --enable_batching --port=9000 --model_name=mnist  --model_base_path=/tmp/mnist_model/"`


Run the client inside the docker

`docker exec -it tensorflow-serving-gpu /serving/bazel-bin/tensorflow_serving/example/mnist_client --num_tests=1000 --server=localhost:9000 --concurrency=10`

Run the tests:

`docker exec -it tensorflow-serving-gpu bash -c "cd /serving && bazel test tensorflow_serving/...""`

Output should be as follows:

```
INFO: Elapsed time: 972.017s, Critical Path: 883.03s
//tensorflow_serving/batching:batch_scheduler_retrier_test               PASSED in 0.2s
//tensorflow_serving/batching:batching_session_test                      PASSED in 0.6s
//tensorflow_serving/batching:streaming_batch_scheduler_test             PASSED in 4.1s
//tensorflow_serving/batching/test_util:puppet_batch_scheduler_test      PASSED in 0.2s
//tensorflow_serving/core:aspired_version_policy_test                    PASSED in 0.2s
//tensorflow_serving/core:aspired_versions_manager_benchmark             PASSED in 29.6s
//tensorflow_serving/core:aspired_versions_manager_builder_test          PASSED in 0.6s
//tensorflow_serving/core:aspired_versions_manager_test                  PASSED in 6.1s
//tensorflow_serving/core:availability_preserving_policy_test            PASSED in 0.2s
//tensorflow_serving/core:basic_manager_test                             PASSED in 8.4s
//tensorflow_serving/core:caching_manager_test                           PASSED in 0.3s
//tensorflow_serving/core:dynamic_source_router_test                     PASSED in 0.2s
//tensorflow_serving/core:loader_harness_test                            PASSED in 0.2s
//tensorflow_serving/core:log_collector_test                             PASSED in 0.1s
//tensorflow_serving/core:manager_test                                   PASSED in 0.5s
//tensorflow_serving/core:request_logger_test                            PASSED in 0.1s
//tensorflow_serving/core:resource_preserving_policy_test                PASSED in 0.1s
//tensorflow_serving/core:servable_data_test                             PASSED in 0.1s
//tensorflow_serving/core:servable_id_test                               PASSED in 0.2s
//tensorflow_serving/core:servable_state_monitor_test                    PASSED in 0.3s
//tensorflow_serving/core:server_request_logger_test                     PASSED in 0.9s
//tensorflow_serving/core:simple_loader_test                             PASSED in 0.1s
//tensorflow_serving/core:source_adapter_test                            PASSED in 1.2s
//tensorflow_serving/core:source_router_test                             PASSED in 2.2s
//tensorflow_serving/core:static_manager_test                            PASSED in 0.4s
//tensorflow_serving/core:static_source_router_test                      PASSED in 0.1s
//tensorflow_serving/core:storage_path_test                              PASSED in 0.1s
//tensorflow_serving/model_servers:server_core_test                      PASSED in 8.8s
//tensorflow_serving/resources:resource_tracker_test                     PASSED in 0.1s
//tensorflow_serving/resources:resource_util_test                        PASSED in 0.1s
//tensorflow_serving/servables/hashmap:hashmap_source_adapter_test       PASSED in 0.1s
//tensorflow_serving/servables/tensorflow:bundle_factory_util_test       PASSED in 0.7s
//tensorflow_serving/servables/tensorflow:classifier_test                PASSED in 0.4s
//tensorflow_serving/servables/tensorflow:curried_session_test           PASSED in 0.3s
//tensorflow_serving/servables/tensorflow:get_model_metadata_impl_test   PASSED in 30.6s
//tensorflow_serving/servables/tensorflow:multi_inference_test           PASSED in 30.3s
//tensorflow_serving/servables/tensorflow:predict_impl_test              PASSED in 31.1s
//tensorflow_serving/servables/tensorflow:regressor_test                 PASSED in 0.3s
//tensorflow_serving/servables/tensorflow:saved_model_bundle_factory_test PASSED in 0.6s
//tensorflow_serving/servables/tensorflow:saved_model_bundle_source_adapter_test PASSED in 0.2s
//tensorflow_serving/servables/tensorflow:session_bundle_factory_test    PASSED in 0.4s
//tensorflow_serving/servables/tensorflow:session_bundle_source_adapter_test PASSED in 0.2s
//tensorflow_serving/servables/tensorflow:simple_servers_test            PASSED in 1.1s
//tensorflow_serving/servables/tensorflow:util_test                      PASSED in 0.2s
//tensorflow_serving/sources/storage_path:file_system_storage_path_source_test PASSED in 0.5s
//tensorflow_serving/sources/storage_path:static_storage_path_source_test PASSED in 0.2s
//tensorflow_serving/util:any_ptr_test                                   PASSED in 0.1s
//tensorflow_serving/util:class_registration_test                        PASSED in 0.1s
//tensorflow_serving/util:cleanup_test                                   PASSED in 0.2s
//tensorflow_serving/util:event_bus_test                                 PASSED in 0.2s
//tensorflow_serving/util:fast_read_dynamic_ptr_benchmark                PASSED in 27.5s
//tensorflow_serving/util:fast_read_dynamic_ptr_test                     PASSED in 0.3s
//tensorflow_serving/util:inline_executor_test                           PASSED in 0.2s
//tensorflow_serving/util:observer_test                                  PASSED in 0.3s
//tensorflow_serving/util:optional_test                                  PASSED in 0.1s
//tensorflow_serving/util:retrier_test                                   PASSED in 0.1s
//tensorflow_serving/util:threadpool_executor_test                       PASSED in 1.3s
//tensorflow_serving/util:unique_ptr_with_deps_test                      PASSED in 0.1s

Executed 58 out of 58 tests: 58 tests pass.

```

Note: device names may be different on your machine. This works with nvidia.


Issues found so far for image building:

- https://github.com/tensorflow/serving/issues/318

- https://github.com/tensorflow/serving/issues/295

- https://github.com/tensorflow/serving/issues/327

Other interesting issues for serving:

- https://github.com/tensorflow/serving/issues/380

Tensorflow official docker images:
https://github.com/tensorflow/tensorflow/blob/master/tensorflow/tools/docker/README.md

(For that imagesp port 8888 is for ipython notebooks and port 6006 is for TensorBoard).


Our images created for tensorflow serving are huge:
```
REPOSITORY                      TAG                 IMAGE ID            CREATED             SIZE
skonto/tensorflow-serving-cpu   latest              140e2f915962        47 minutes ago      12.9 GB
skonto/tensorflow-serving-gpu   latest              1abf62f5b746        2 hours ago         9.83 GB
```

TODO: Probably nvidia-docker setup is more minimal but lacks documentation. Another option is to try optimize the docker files.
