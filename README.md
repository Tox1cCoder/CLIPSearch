# Image Search using CLIP

Retrieve images based on a query (text or image), using Open AI's pretrained CLIP model.

Text as query.

![](https://i.imgur.com/5y7NaHU.png)

Image as query.

![](https://i.imgur.com/xYJ6SnQ.png)

## Installation

### Install dependencies

```
pip install -e . --no-cache-dir
```

### Download the Unsplash dataset

```
python scripts/download_unsplash.py --image_width=480 --threads_count=32
```

This will download and extract a zip file that contains the metadata about the photos in the dataset. The script will use the URLs of the photos to download the actual images to `unsplash-dataset/photos`. The download may fail for a few images (see [this issue](https://github.com/unsplash/datasets/issues/37#issuecomment-854711074)). Since CLIP will downsample the images to 224 x 224 anyway, you may want to adjust the width of the downloaded images to reduce storage space. You may also want to increase the `threads_count` parameter to achieve a faster performance.

### Create index and upload image feature vectors to Elasticsearch

```
python scripts/ingest_data.py
```

The script will download the pretrained CLIP model and process the images by batch. It will use GPU if there is one.

### Build Docker image

Build Docker image for AWS Lambda.

```
docker build --build-arg AWS_ACCESS_KEY_ID=YOUR_AWS_ACCESS_KEY_ID \
             --build-arg AWS_SECRET_ACCESS_KEY=YOUR_AWS_SECRET_ACCESS_KEY \
             --tag clip-image-search \
             --file server/Dockerfile .
```

Run the Docker image as a container.

```
docker run -p 9000:8080 -it --rm clip-image-search
```

Test the container with a POST request.

```
curl -XPOST "http://localhost:9000/2015-03-31/functions/function/invocations" -d '{"query": "two dogs", "input_type": "text"}'
```

### Run Streamlit app

```
streamlit run streamlit_app.py
```
