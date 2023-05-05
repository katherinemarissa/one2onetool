# one2onetool
This CI/CD pipeline is triggered on new commits and performs the following:
1. Build and Run tests on the application
2. Containerise and deploy the application on a public cloud instance
3. "Staging" branch uses "Questions-test.json" as input data file, while "Release" branch uses "Questions.json" as input data file
4. Versioning to differentiate builds
5. If any of the tasks fail at any point, the pipeline stops and an email alert is sent with relevant information
