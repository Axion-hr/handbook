# Naming conventions

## Git repository naming

<client_abbreviation>-<project_abbreviation>-<be/fe>-<service_name>

Above pattern might change if it is too long and causes issues down the road (resources in AWS cannot have extremely long names).

For automation reasons, make sure artifact id in the `pom.xml` file matches repository name. Those two names are used for referencing related varaibles in AWS SSM, and artifactId is used for pushing containers to ECR. Most of the automation assumes repository name when performing various activities.
