# Lecture-5 jenkins


##  Jenkins Pipeline

=> Jenkins pipeline is a way to define CI CD process as a code.

=> The whole CI CD workflow we will define as a code in pipeline.

=> when we are dealing with complex CI CD process then pipelines are highly recommended.

=> Pipeline contains set of stages to perform CI CD

		Stage-1: Clone Git Repo

		Stage-2: Maven Build

		State-3: Code Review

		Stage-4 : Artifact Upload

		Stage-5: Build Docker Image

		Stage-6: Push Image to Registry

		Stage-7: Deploy App in K8S

		Stage-8: Send Email Notification

=> We can create jenkins pipelines in 2 ways

			1) Declarative Pipeline

			2) Scripted (groovy) Pipeline


 ### Jenkins Declarative Pipeline           