# logitech_interview
Case Study for Logitech Interview

# logitech_interview
Case Study for Logitech Interview

***CI/CD Pipeline Overview***:

1. Code Check-in: Engineers commit code to feature branches, which triggers automated jobs (CI pipeline).
2. Build and Test: The system checks out the code, installs dependencies, runs unit tests, and builds the application for Windows, Linux, and macOS.
3. Code Quality and Coverage: The CI pipeline runs static code analysis, unit tests, and code coverage metrics.
4. Docker Build: The pipeline builds Docker images for the backend and frontend.

***Deployment***:

1. Frontend: Deploys to a CDN (e.g., AWS S3 or CloudFront). (Only Jenkins)
2. Backend: Deploys the Docker container to Kubernetes on the cloud/on-permises (e.g., AWS EKS, GCP GKE).
3. Production Branch: Conditional deployment to production only happens when changes are merged into the master/main branch.
4. Notifications: On build failure or deployment success, notifications are sent to teams (e.g., via Slack or email).

***Code Signing Approach***:

**Queue-Based Throttling Mechanism**

1. Queue Creation: Implement a queue that holds the files for signing, which is controlled by a signing worker.
2. Throttling: The signing server processes a fixed number of files at a time (e.g., 2-3 builds simultaneously).
3. Worker System: Workers poll the queue and fetch a batch of files for signing. Once signed, the files are removed from the queue, and new ones are picked up for signing.
4. Monitor & Scaling: Monitor the queue and dynamically scale the signing server to handle increased loads when necessary.

**Batched Signing Process**

1. Batch Files: At the end of the build process, group the 100 files into a single archive (e.g., ZIP or TAR file).
2. Send in Batches: Send this archive to the signing server, where it is signed as a single operation.
3. Extract and Distribute: After the signing is done, extract the files from the archive, and distribute them.


**Pipeline Segmentation**

1. Split Build Process: Split the build pipeline into multiple steps where each step generates a subset of files.
2. Sign After Each Step: After each step, sign the generated subset of files immediately, instead of waiting for the entire build to finish.
3. Merge Signed Files: Once all steps are complete, merge the signed files together.


**Cached Signing and Incremental Signing**

1. Check for Changes: During the build, compare the generated files with previous builds to identify unchanged files.
2. Sign Only Changed Files: Send only the modified files to the signing server.
3. Use Cache: For files that haven’t changed, use the previously signed versions from the cache.