# TrenditPosSDK

## Installation

## Step 1
```sh
#Create a github.properties file in the root folder of android. Path should be like below: E.g. "mySampleApp/android/github.properties"

# Add the following in github.properties:
USERNAME_GITHUB=MyUsernameOnGithub
TOKEN_GITHUB=TokenFromGithubClassicToken
```

## Step 2: build.gradle (project level)
Add the following to build.gradle(project level) file
```sh


buildscript {
    repositories {
      ...
    }
    dependencies {
      ...
    }
}

allprojects {
    def githubPropertiesFile = rootProject.file("github.properties")
    def githubProperties = new Properties()
    githubProperties.load(new FileInputStream(githubPropertiesFile))

    repositories {
        ...
                
        maven {
          url 'https://gitlab.com/api/v4/projects/4128550/packages/maven'
        }
        maven {
          name "GitHubPackages"
          url 'https://maven.pkg.github.com/Blusalt-FS/BlusaltTrenditPosSDK'

          credentials {
            username githubProperties['USERNAME_GITHUB']
              password githubProperties['TOKEN_GITHUB']
            }
       }
    }
}

```


## Step 3: build.gradle (app)
All the following to build.gradle (app level)

```sh

dependencies{
  implementation 'net.blusalt:blusalthorizonlib:1.2-4'

}
```


## Usage
```

## Java
```java
class MainActivity extends AppActivityCompact {
    @override
    void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main_pos);
            new Pos().init(getApplication());
            new Pos().configurePOS("api key", getApplicationContext(), listener);

            Pos.getINSTANCE().startPOSsdk(getApplicationContext(), 10.00, "", new TransactionCompletedCallBack() {
                @Override
                public void onSuccess(String terminalResponse) {

                    TerminalInfoProcessor response = new Gson().fromJson(terminalResponse, TerminalInfoProcessor.class);
                    Log.e("Result", terminalResponse);

                    runOnUiThread(() -> {
                        Toast.makeText(getApplicationContext(), response.responseDescription, Toast.LENGTH_SHORT).show();

                    new AlertDialog.Builder(MainActivity.this)
                            .setTitle(String.valueOf(response.responseCode))
                            .setMessage(response.responseDescription)
                            .setPositiveButton("Okay", new DialogInterface.OnClickListener() {
                                @Override
                                public void onClick(DialogInterface dialog, int which) {
//                                    new Pos().prepareForPrinter(getApplicationContext(), response);
                                    dialog.dismiss();
                                }
                            })
                            .show();
                    });
                }

                @Override
                public void onFailure(int statusCode, String errorObject) {

                    runOnUiThread(() -> {
                        Toast.makeText(getApplicationContext(), errorObject, Toast.LENGTH_SHORT).show();
                    new AlertDialog.Builder(MainActivity.this)
                            .setTitle(String.valueOf(statusCode))
                            .setMessage(errorObject)
                            .setPositiveButton("Okay", new DialogInterface.OnClickListener() {
                                @Override
                                public void onClick(DialogInterface dialog, int which) {
                                    dialog.dismiss();
                                }
                            })
                            .show();
                    });
                }
            });

    }

   TerminalKeyParamDownloadListener listener = new TerminalKeyParamDownloadListener() {
        @Override
        public void onSuccess(String message) {
            Log.e("TAG: ", "Result: " + message);
            Toast.makeText(getApplicationContext(), message, Toast.LENGTH_SHORT).show();
        }

        @Override
        public void onFailed(String error) {
            Log.e("TAG: ", "Result: " + error);
            Toast.makeText(getApplicationContext(), error, Toast.LENGTH_SHORT).show();
        }
    };
}


