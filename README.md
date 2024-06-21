# Capstone Movie ML API
This is a repository containing the deployment of the machine learning model used by the backend API. The model runs in Python and is deployed using a Flask web server listening on port 5000.


## Disclaimer
You can provide a `.env` file to set an API key for the model. Just provide an `API_KEY` environmental variable and the API will automatically use it to validate incoming requests.
All requests is validated through a `key` query param in the url like so [https://modelapiurlhere.com?key=this+api+key](https://modelapiurlhere.com?key=this+api+key)

> [!IMPORTANT]
> If no `API_KEY` environmental variable is provided, the server will launch without validating requests.


## API Documentation
This assumes that the API is running on [http://localhost:5000](http://localhost:5000), if your deployed API is in another location then feel free to change the url.

### Check (GET)

    http://localhost:5000/check

This always returns a 200 HTTP response, use it to check if the server is up or not. The response schema is as follows:
```
{
    message: "api is up and running"
}
```

### Recommend (POST)

    http://localhost:5000/recommend

This endpoint receives a POST request with a json payload and returns the model output from that payload. The payload schema is as follows:
```
{
    "year": 2000,
    "runtime": 100,
    "genre": "sci-fi",
    "director": "",
    "star": "",
    "rating": 9.0,
    "votes": 1000
}
```

- year: what preferred year did the movie come out
- runtime: the length of the movie
- genre: the genre of the movie (**SINGULAR**)
- director: the preferred director
- star: the preferred actors in the movie
- rating: the preferred movie ratings
- votes: the preferred number of votes/ratings

> [!IMPORTANT]
> The `director` and `star` property is **optional**, but the other fields are **mandatory**.

The endpoint then returns an json object with the following schema:

```
{
    "recommendations": [
        {
            "combined_features": "Sci-Fi Gerard Johnstone Allison Williams, \nViolet McGraw, \nRonny Chieng, \nAmie Donald",
            "director": "Gerard Johnstone",
            "genre": "Sci-Fi",
            "movie_name": "M3GAN",
            "rating": 6.4,
            "runtime": 102,
            "similarity_score": 0.0,
            "star": "Allison Williams, \nViolet McGraw, \nRonny Chieng, \nAmie Donald",
            "votes": 62458.0,
            "year": 2022
        },
        .
        .
        .
    ],
    "status": "success"
}
```

## Local Installation
Clone the repository

    git clone https://github.com/entertainmeproject/ml-api-movie.git

Move into the directory

    cd ml-api-movie

Run the application

    python main.py

The machine learning model API will run on [http://localhost:5000](http://localhost:5000) by default.

## Containerize The Application
Run the following command in the directory where the model API is located.

    docker build -t container_tag .

Run the built Docker image

## GCP Installation
Clone the repository

    git clone https://github.com/entertainmeproject/ml-api-movie.git

Move into the directory

    cd ml-api-movie

Containerize the application as follows. 
> [!NOTE]
> Replace anything fully capitalized and preceded with a `$` sign with the appropriate names

    docker build -t $REGION-docker.pkg.dev/$GCP_PROJECT/$ARTIFACT_REGISTRY_REPO/$IMAGE:$TAG .

Make sure that you've created a repository in artifact registry inside the google cloud project that you can use to store the docker images.

Afterwards, push the image to artifact registry with

    docker push $REGION-docker.pkg.dev/$GCP_PROJECT/$ARTIFACT_REGISTRY_REPO/$IMAGE:$TAG

If everything is successful, you should be able to deploy the ML API to Cloud Run. Assign at least 1GB of RAM to the instance so it runs smoothly.
