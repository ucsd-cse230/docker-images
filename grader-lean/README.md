# grader-lean

Docker image to use for [external grading in PrairieLearn](https://prairielearn.readthedocs.io/en/latest/externalGrading/).
It comes with `python3.12` and `leanprover/lean4:4.15.0`.

```bash
docker build --pull --rm -f "Dockerfile" -t <username>/grader-lean:latest "."
docker image push <username>/grader-lean:latest
```
