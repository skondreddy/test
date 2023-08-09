# JSON Parsing in Java
To maintain consistency we should all use the same package to do JSON parsing.
This will make it easier to do updates to the JSON package if all parsing is
done using the same package. 

We currently use ***Jackson*** by FasterXML, v2.14 as of 01/25/2023 

## Usage
### Shell Scripts
Import $JACKSON_JARS to the class path in the job's run script. 

Example: [getIIRAccessToken.sh](https://gitlab.com/sesco/legacy/user-applications/jobs_bin/-/blob/4db45928f2df8c22363b47b3653737fec27c3679/iir/getIIRAccessToken.sh)
### Java
For new classes we should use Jackson Object annotations. This allows you to map the JSON fields directly to the object member values. 

Example: [com.sesco.iir.json.AccessToken](https://gitlab.com/sesco/legacy/user-applications/base/-/blob/00ffbfa8ebf12a022eac2e8ef8f88762ce6b493c/src/com/sesco/iir/json/AccessToken.java)

For more info, see [Jackson annotations documentation](https://github.com/FasterXML/jackson-annotations#usage-simple)

## Future Versions
To change the version of Jackson that is being used by jobs you will need to update JACKSON and JACKSON_JARS in [setSESCOenv.sh](https://gitlab.com/sesco/legacy/user-applications/jobs_bin/-/blob/master/setSESCOenv.sh) in the [jobs_bin](https://gitlab.com/sesco/legacy/user-applications/jobs_bin) Git repo. These are environment variables that will be exported to the job script via the JACKSON_JARS variable. 

## Other Links
- [Github](https://github.com/FasterXML/jackson)
- [Tutorials](https://github.com/FasterXML/jackson#tutorials)

## Notes
- Links in this document to files in Sesco repositories are to current Git versions of those files ***as of 01/25/2023***.  This is done intentionally to preserve the examples, but may not reflect the latest versions of these files.
