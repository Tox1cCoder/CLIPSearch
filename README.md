# Image Search using CLIP

## Installation

### Install dependencies

```
pip install -e . --no-cache-dir
```

### Download the Unsplash dataset

```
python scripts/download_unsplash.py --image_width=480 --threads_count=32
```

### Create index and upload image feature vectors to Elasticsearch

```
python scripts/ingest_data.py
```

### Build Docker image

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
