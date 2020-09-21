# Set custom parameters

In a DataStream job, you can obtain custom parameters from the main function as needed.

## Method to obtain and set custom parameters

To obtain a custom parameter from the main function, define the custom parameter in the format of `paramName=paramValue` in a DataStream job.

-   `paramName`: the name of the custom parameter.
-   `paramValue`: the value of the custom parameter.

**Note:** You can define multiple custom parameters in a DataStream job.

## Example of obtaining and setting custom parameters

**Note:** After you set `blink.job.name=jobnametest` on the development page, you can use the following code to assign the `jobnametest` string to the `jobName` variable.

```
import org.apache.flink.api.java.utils.ParameterTool;
import java.io.StringReader;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.util.Properties;

public class getParameterExample {
    public static void main(String[] args)throws Exception {
        final String jobName;
        final ParameterTool params = ParameterTool.fromArgs(args);

        /*You must enter configFile to read parameters in configFile. */
        String configFilePath = params.get("configFile");

        /*Create a Properties object to save parameter values that you set in the system. */
        Properties properties = new Properties();

        /*Load parameter values that you set in the system to the Properties object. */
        properties.load(new StringReader(new String(Files.readAllBytes(Paths.get(configFilePath)), StandardCharsets.UTF_8)));

        /*Obtain the jobName parameter. */
        jobName = (String) properties.get("blink.job.name");
    }
}
```

**Note:** The preceding example only demonstrates how to obtain and use custom parameters in the main function. To use custom parameters in a Flink operator, you must add the following code to the preceding code to convert custom parameters to global job parameters. Then, you can obtain the corresponding parameters in a Flink operator by using the getproperty method of the Configuration class.

```
env.getConfig().setGlobalJobParameters(ParameterTool.fromPropertiesFile(configFilePath));
```

