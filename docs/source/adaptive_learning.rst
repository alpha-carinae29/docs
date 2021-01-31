Adaptive Learning
=================

Adaptive learning is the process of customization of object detection models with user-provided data and environments. For more information, please visit our `blog post <https://neuralet.com/article/adaptive-learning/>`_.

Client
^^^^^^

Neuralet adaptive learning service includes client/server side. You can start an adaptive learning task on the cloud and get the model after training on the client-side.

Run the docker container based on your device and the below commends inside the container: ::

    cd services/adaptive-learning/client

**#Step 1:**

Create an :code:`input.zip` file from the video file you want to feed to Adaptive Learning. ::

    zip input.zip PATH_TO_VIDEO_FILE

**#Step 2:**

Upload the zip file and get a unique id: ::

    python3 client.py upload_file --file_path FILE_PATH

**#Step 3:**

Add the previous step's unique id to the :code:`UploadUUID` field and the video file name to the :code:`VideoFile` field of the config file. You can find a more comprehensive explanation of the config file and its fields in the next section. Note: You can use the sample config file in :code:`configs/sample_config.ini`

**#Step 4:**

Initiate a new job and get your job's ID: ::

    python3 client.py train --config_path CONFIGPATH

**#Step 5:**

Get a job status (enter the job id at JASKID) ::

    python3 client.py get_status --job_id JOBID

**#Step 6:**

Download the trained model whenever the job has been finished. ::

    python3 client.py download_file --job_id JOBID

Adaptive Learning Config File
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To customize the Adaptive Learning framework based on your needs, you must configure the sample config file on :code:`configs/` directory. There is a brief explanation of each parameter of config files in the following table:

.. csv-table:: a title
    :header: "Parameter", "Options", "Comments"
    :widths: 10, 20, 20

    "Teacher/MinScore", "a float number between 0 and 1", "The teacher model threshold for detecting an object."
    "Teacher/UploadUUID", "a UUID", "Unique id of uploaded input.zip file."
    "Teacher/VideoFile", "string", "Name of the video you zipped and uploaded."
    "Teacher/MinDetectionPerFrame", "an integer number", "The teacher stores the frame only if it detects at least this many objects; otherwise, it discards the frame."
    "Teacher/SaveFrequency", "An integer bigger than 0", "The teacher model will store video frames and the corresponding predictions with this frequency."
    "Teacher/PostProcessing", "One of :code:`'background_filter'` or :code:`' '` ", "Background filter will apply a background subtraction algorithm on video frames and discards the bounding boxes in which their background pixels rate is higher than a defined threshold."
    "Teacher/ImageFeature", "One of the :code:`'foreground_mask'`, :code:`'optical_flow_magnitude'`, :code:`'foreground_mask && optical_flow_magnitude'` or :code:`' '`", "This parameter specifies the type of input feature engineering that will perform for training. :code:`'foreground_mask'` replaces one of the RGB channels with the foreground mask. :code:`'optical_flow_magnitude'` replaces one of the RGB channels with the magnitude of optical flow vectors and, :code:`'foreground_mask && optical_flow_magnitude'` performs two feature engineering technique at the same time as well as changing the remaining RGB channel with the grayscale transformation of the frame. For more information about feature engineering and its impact on the model's accuracy, visit `our blog <https://neuralet.com/article/adaptive-learning/>`_ ."
    "Student/BatchSize", "An integer bigger than 0", "The student's training batch size"
    "Student/Epochs", "An integer bigger than 0",  "The student's training number of epochs in each round"
    "Student/ValidationSplit",  "An float between 0 and 1", "the portions of data which will be used for validation in each training round."
    "Student/QuantizationAware", "true or false", "whether to train the student model with quantization aware strategy or not. This is especially useful when you want to deploy the final model on an edge device that only supports :code:`Int8` precision like Edge TPU. By applying quantization aware training the App will export a :code:`tflite` too."

