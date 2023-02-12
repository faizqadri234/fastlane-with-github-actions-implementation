# fastlane-with-github-actions-implementation

You Just need to follow all the below stpes to automate your releases with fastlane and github actions in no time 

This component is actually a semi circle slider but it is designed in this way that you can use it as color picker too.

## 1. Fastlane Installation:

```tsx
xcode-select --install
brew install ruby
sudo gem install fastlane -NV
```

## 2. Fastlane Setup:

```tsx
cd you_project_directory
fastlane init
```
### Now select option 2:
```tsx
[07:30:10]: What would you like to use fastlane for?
1. 📸  Automate screenshots
2. 👩‍✈️  Automate beta distribution to TestFlight
3. 🚀  Automate App Store distribution
4. 🛠  Manual setup - manually setup your project to automate your tasks
```

### Now enter your apple developer credetials:
```tsx
[13:03:04]: Please enter your Apple ID developer credentials
[13:03:04]: Apple ID Username:
<YOUR_EMAIL>
[13:04:36]: Logging in...
```

### If you haven't created App ID yet, fastlane can do it for you. Press `y` if you want to create App ID, else press `n`. 
```tsx
Logging in with your Apple ID was successful
[00:56:27]: Checking if the app ‘your_bundle_id’ exists in your Apple Developer Portal…
[00:56:27]: It looks like the app ‘your_bundle_id’ isn’t available on the Apple Developer Portal
[00:56:27]: for the team ID ‘your_team_id’ on Apple ID ‘your_apple_id’
[00:56:27]: Do you want fastlane to create the App ID for you on the Apple Developer Portal? (y/n)
```
### If you haven't created an App yet, then fastlane can do it for you. Press `y` if you want to create an App, else press `n`
```tsx
App Name: your_project_name
[01:01:00]: Creating new app ‘your_project_name’ on the Apple Dev Center
[01:01:01]: Created app your_app_id
[01:01:02]: Finished creating new app ‘your_project_name’ on the Dev Center
[01:01:02]: ✅ Successfully created app
[01:01:02]: Checking if the app ‘your_bundle_id’ exists on App Store Connect…
[01:01:03]: Looks like the app ‘your_bundle_id’ isn’t available on App Store Connect
[01:01:03]: for the team ID ‘your_team_id’ on Apple ID ‘your_apple_id’
[01:01:03]: Would you like fastlane to create the App on App Store Connect for you? (y/n)
```
### After installing dependecies, two files `Fastfile` and `Appfile` will be created in `your_project/Fastlane`.

## 3. Fastlane Match Configuration:

Fastlane match initialisation:

```tsx
cd Fastlane
fastlane match init
```
Then select option 1 `git`

```tsx
[01:00:00]: fastlane match supports multiple storage modes, please select the one you want to use:
1. git
2. google_cloud
3. s3
?
```
Now give the URL of newly created repo for certificate and provisioning profile. If you havn't created repo for certificate and provisioning profile then please do it.

```tsx
[01:00:00]: Please create a new, private git repository to store the certificates and profiles there
[01:00:00]: URL of the Git Repo: <YOUR_CERTIFICATES_REPO_URL>
```
Now you will get the `Matchfile` with the following code:

```tsx
git_url("https://github.com/gitusername/certificates")
storage_mode("git")
type("appstore")
```
## 4. Configure Fastfile and Appfile:

Now in `your_project/Fastlane/Fastfile` replace your Fastfile and Appfile with my `Fastfile` and `Appfile` provided inside `Fastlane` folder in this repo.

Now change the following things according to your project. Like:
```tsx
increment_build_number(xcodeproj: "YourApp.xcodeproj")
```

```tsx
gym(
      configuration: "Release",
      workspace: "YourApp.xcworkspace",
      scheme: "YourApp",
      export_method: "app-store",
      output_directory: './build',
      output_name: "your_app_name.ipa",
      export_options: {
        provisioningProfiles: {
            DEVELOPER_APP_ID => PROVISIONING_PROFILE_SPECIFIER
        }
      }
    )
```
```tsx
pilot(
      apple_id: "#{DEVELOPER_APP_ID}",
      app_identifier: "#{DEVELOPER_APP_IDENTIFIER}",
      skip_waiting_for_build_processing: true,
      skip_submission: true,
      distribute_external: false,
      notify_external_testers: false,
      ipa: "./build/your_app_name.ipa"
    )
```
```tsx
git_commit(
      path: [
          "./YourApp.xcodeproj/project.pbxproj",
          "./YourApp/Info.plist",
        ],
       message: "Increment Build number"
     )
    version = get_version_number(
      xcodeproj: "YourApp.xcodeproj",
      target: "YourAppTarget"
     )
    build_num = get_build_number(
      xcodeproj: "YourApp.xcodeproj",
    )
```
And now change the `Appfile` according to project too

```tsx
app_identifier("xxx.xxxxxxxx.xxxxxx") # The bundle identifier of your app
apple_id("xxxxx@xxxx.xxx") # Your Apple email address

team_id("xxxxxxxxxxxx") # Developer Portal Team ID

# For more information about the Appfile, see:
# https://docs.fastlane.tools/advanced/#appfile

```

## 5. Environment File Setup:

I have provided the `.env` file in this repo. You just to put this file inside `your_project/Fastlane`

### Initialisation of all the environment variables:
#### Lets create app store connect Api Key first:
1. Login to App Store Connect
2. Click on `Users and Access`

<img src="https://github.com/faizqadri234/fastlane-with-github-actions-implementation/blob/main/Assets/user_and_access.png" width="375">

3. Click on `keys` above

<img src="https://github.com/faizqadri234/fastlane-with-github-actions-implementation/blob/main/Assets/keys.png" width="375">

4. Click on `generate Api Key` button

<img src="https://github.com/faizqadri234/fastlane-with-github-actions-implementation/blob/main/Assets/generate_keys.png" width="375">

5. Now enter the `name` and slect the `role` for the key. And then click on `generate` button

<img src="https://github.com/faizqadri234/fastlane-with-github-actions-implementation/blob/main/Assets/write_credentials.png" width="375">

6. Now copy `issuer ID`, `key ID` and download `Api key`. And make sure to save the `Api Key` because you can only download it once.

<img src="https://github.com/faizqadri234/fastlane-with-github-actions-implementation/blob/main/Assets/created_key.png" width="375">

Now placed <b>`issuer ID`</b> and <b>`key ID`</b> against <b>`APPLE_ISSUER_ID` and `APPLE_KEY_ID`</b> in .env file respectively. And now drag and drop your downloaded Api key in Visual Studio Code or any editor, you will get Apple key content. Placed this content against 'APPLE_KEY_CONTENT' in .env file

<b>1.<u> *APPLE_ISSUER_ID:*</u></b> This is the appstore connect <b>`issuer ID`</b>

<b>2.<u> *APPLE_KEY_ID:*</u></b> This is the appstore connect <b>`Key ID`</b>

<b>3.<u> *APPLE_KEY_CONTENT:*</u></b> This is the appstore conenct api key content

<b>4.<u> *APP_STORE_CONNECT_TEAM_ID:*</u></b> This is the ID of your appstore connect team

<b>5.<u> *DEVELOPER_APP_ID:*</u></b> In App store connect, go to your app and then click on <b>`App Information`</b> in side menu. Scroll down to <b>`general information`</b> of your app and look for <b>`Apple ID`</b>

<b>6.<u> *DEVELOPER_APP_IDENTIFIER:*</u></b> This is your app's <b>`Bundle ID`</b>

<b>7.<u> *DEVELOPER_PORTAL_TEAM_ID:*</u></b> This is your developer portal <b>`team ID`</b> if you are a part of multiple teams

<b>8.<u> *FASTLANE_APPLE_ID:*</u></b> This is your <b>`Apple ID`</b>

<b>9.<u> *MATCH_PASSWORD:*</u></b> This is the passphrase which you will put while running your match script. Leave it for now. When you will run the fastfile script then match script will also be executed and while running it you will asked to provide a passphrase. After putting the passphrase, please save the pass phrase inside `.env` file too.

<b>10.<u> *PROVISIONING_PROFILE_SPECIFIER:*</u></b> This is the name of your provisioning profile. You can write it as <b>`match AppStore xxx.xxxxxxxx.xxxxxx`</b>

<b>11.<u> *TEMP_KEYCHAIN_PASSWORD:*</u></b> This is the password of your temporary keychain.

<b>12.<u> *TEMP_KEYCHAIN_USER:*</u></b> This is the name of your temporary keychain.

<b>13.<u> *GIT_AUTHORIZATION:*</u></b> This is the access token of your git account. You can create it from <b>`Settings/Developer settings`</b>

<img src="https://github.com/faizqadri234/fastlane-with-github-actions-implementation/blob/main/Assets/access_token.png" width="375">



**MIT Licensed**