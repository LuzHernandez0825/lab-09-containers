# Lab 09: Knowledge check

**Answer each question below it, in your own words, in one to three sentences.** Replace the `...` below **Your answer:** with your text.

## 1. Credentials in the image?

Assume you want to share your image with others and make it publicly available on `ghcr.io`, so that anyone can pull it and run the analysis. A teammate suggests adding `COPY .aws /home/app/.aws` to the `Dockerfile`, so that nobody has to type `-v ~/.aws:/home/app/.aws:ro` any more. (This is a thought experiment: keep your own package private.)

**Your answer:** Give two reasons why this is a bad idea. Think of who could then read your credentials, and of what happens when your Learner Lab session ends.
It's a bad idea because the credentials are the only thing protecting your account. By sharing them in an image it ends up risking the security of your account. It also very easy to extract the history of the images even if they are deleted by anyone that has access to that image. Placing the Learner Lab credentials in the image as well causes an issue because the image is a snapshot of the time working with it and the Learner Lab credentials expire. Meaning that the image would hold expired credentials.

## 2. Where did the Atlas password come from?

In Step 4, you ran the analysis once with `-v ~/.aws:/home/app/.aws:ro` only, without the three `-e` options. The container still wrote to your Atlas cluster.

*Hint:* Read `get_atlas_settings()` in `src/book_analysis/analyze.py`.

**Your answer:** Explain, step by step, where the container found the Atlas settings in that run.
The container was able to write to the atlas cluster because it had another path to follow via the get_atlas_settings(). The   first path in the function checked the environment variables which were not in the container because they were not passed in with the -e flag and that triggered the second path. My laptop's  ~/.aws folder was mounted with -v to /home/app/.aws inside the container, where the credentials were read in order to verify access to the AWS SSM Parameter Store and pull down the mongodb_credentials.

## 3. Same tag, different image

Run `docker images book-analysis`, and note the image ID of `book-analysis:0.1.0`. Then change the `description` in the `LABEL` of your `Dockerfile`, build again with the same command, `docker build -t book-analysis:0.1.0 .`, and run `docker images book-analysis` again.

**Your answer:** Paste the image ID from before and after the rebuild. Why is it a problem for someone who uses your image that `0.1.0` now names a different image, and what should you have done instead?
IMAGE                 ID             DISK USAGE   CONTENT SIZE   EXTRA
book-analysis:0.1.0   79adfa677aa3        328MB         82.7MB        
IMAGE                 ID             DISK USAGE   CONTENT SIZE   EXTRA
book-analysis:0.1.0   24e5b2d9caf8        328MB         82.7MB        
It is an issue because changing the label, even just a small one word change, changes the ID entirely. This is a problem because it breaks the rule of repredoucablility, some people may have the original 79adfa677aa3 image while others have the 24e5b2d9caf8 image. Having both of these images be under 0.1.0 means that the version number no longer refers to a single build but rather a collection. Instead what should have been done is having a different version number for the later version like 0.1.1 or 0.2.0. 

