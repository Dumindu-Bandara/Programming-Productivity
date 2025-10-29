### Converting Docker Image to Apptainer


```bash
# Convert docker image to .tar
docker save myimage:tag -o myimage.tar

# Move .tar file over to new machine and then build the image
apptainer build myimage.sif docker-archive://myimage.tar

# or with a writable directory.
apptainer build --sandbox myimage_sandbox docker-archive://myimage.tar
```