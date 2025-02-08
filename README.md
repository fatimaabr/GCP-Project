
# How to Deploy My Streamlit Poem Generator on GCP

In this guide, we will walk you through how I deployed my **Streamlit app** (which generates poems) to **Google Cloud Platform (GCP)**. The app is built with Python (we used an Two LLMs gpt2 and gpt-neo), and I'm using **Cloud Run** for the deployment. Here are the steps I followed:

## Step 1: Setting Up GCP

### a. Create a GCP Project

- First, I created a new **Google Cloud project** via the [Google Cloud Console](https://console.cloud.google.com/).
- After creating the project, I noted the id-project onyx-etching-450221-s4 because I needed it later.

### b. Enable Cloud Run and Cloud Build

- I needed to enable **Cloud Run** (for deploying the app) and **Cloud Build** (for building the Docker image). I did this by running the following commands in my terminal:

```bash
gcloud services enable run.googleapis.com
gcloud services enable cloudbuild.googleapis.com
```

## Step 2: Prepare the App

### a. Create a `Dockerfile`

- To deploy the app, we wrote a `Dockerfile` that tells GCP how to run my app. Here’s the content of the `Dockerfile`:

```dockerfile
dockerfile = '''
# Use an official Python runtime as a parent image
FROM python:3.9-slim

# Set the working directory in the container
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# Expose port 8501 (default for Streamlit)
EXPOSE 8501

# Run the Streamlit app
CMD ["streamlit", "run", "gcp_app.py"]
'''
```

- This file ensures that the app runs correctly on GCP by installing all necessary dependencies and starting the Streamlit app.

### b. `requirements.txt`

- In this file, we listed all the necessary Python packages for my app. For example:

```
streamlit
pandas
```

## Step 3: Build and Deploy the App

### a. Authenticate with Google Cloud

- I logged into GCP using the following commands:

```bash
!gcloud services enable run.googleapis.com
!gcloud services enable cloudbuild.googleapis.com
!gcloud config set project onyx-etching-450221-s4
```

### b. Build the Docker Image

- I built the Docker image of my app and pushed it to **Google Container Registry** (GCR) using:

```bash
with open("Dockerfile", "w") as file:
    file.write(dockerfile)

!gcloud builds submit --tag gcr.io/onyx-etching-450221-s4/poem-generator .
```

### c. Deploy to Cloud Run

- Once the image was ready, I deployed it to **Cloud Run** using:

```bash
!gcloud run deploy poem-generator \
  --image gcr.io/onyx-etching-450221-s4/poem-generator \
  --platform managed \
  --region us-central1 \
  --allow-unauthenticated
```

- GCP then provided me with a **public URL** where my app was accessible!

## Step 4: Access the App

- After the deployment was successful, I visited the URL provided by GCP to see my **poem generator** app live! 😄

---

## Conclusion

That's it! I followed these steps to successfully deploy my **Streamlit poem generator app** on GCP.

