App42-Tic-Tac-Toe
===========================

# About Game

1. Its a multi-player turn based game.
2. The winning motive of the game is to connect three respective cross or circle in a vertical ,horizontal or digonal direction.
3. When opponent played his turn , a notification message comes to player that now his turn came.

# Runnnig Sample

This is a sample Android gaming app is made by using App42 backened platform. It uses user, storage ,pushNotification and gaming APIs of App42 platform. 
Here are the few easy steps to run this sample app.


1. [Register] (https://apphq.shephertz.com/register) with App42 platform
2. Create an app once you are on Quickstart page after registeration.
3. Goto dashboard and create a new game TicTacToe (Click on Business service manager->game service->add game->)
4. If you are already registered, login to [AppHQ] (http://apphq.shephertz.com) console and create an app from App Manager tab and do step #3 to create a game.
5. To use pushNotification service in your application go to https://code.google.com/apis/console , create a new project here.
6. Click on services option in google console and enable Google Cloud Messaging for Android service.
7. Click on API Access tab and create a new server key for your application with blank server information.
8. Go to AppHQ console and click on Push Notification and select Android setting in Settings option.
9. Select your game and copy server key that is generated by using google api console , and submit it.
10. Download the eclipse project from this repo and import it in the same.
11. Open Constants.java in sample app and give the value of app42APIkey app42SecretKey that you have received in step 2 or 4
12. Build and Run 



# Design Details:

__Initialize Services:__

Initialization has been done in AsyncApp42ServiceApi.java

```
        ServiceAPI sp = new ServiceAPI(Constants.App42ApiKey,
  			Constants.App42ApiSecret);
		this.userService = sp.buildUserService();
		this.storageService = sp.buildStorageService();
		this.pushService = sp.buildPushNotificationService();
```

__Register User:__ First register yourself to play game.
 User registeration has been done in in AsyncApp42ServiceApi.java

```
              User user = userService.createUser(name, pswd, email);
```
__Authenticate User:__ If you alredy registered with App42 than authentication is required .
 User Authenticatation has been done in in AsyncApp42ServiceApi.java

```
             App42Response response = userService.authenticate(
							name, pswd);
```
__Push Service registeration :__ To get Push notification you have to register your device on APP42 using PushNotificationService.

Device Registration is done in MainActivty.java

```
           public void doRegistration(Context context, final String userID) {
			this.context = context;
			GCMRegistrar.checkDevice(context);
			GCMRegistrar.checkManifest(context);
			final String deviceId = GCMRegistrar.getRegistrationId(context);
			if (deviceId.equals("")) {
			//Sender Id is equals to our projectNo generated on Google Api console. 
				GCMRegistrar.register(MainActivity.this, Constants.SENDER_ID);
			} else {
				mRegisterTask = new AsyncTask<Void, Void, Void>() {
					@Override
					protected Void doInBackground(Void... params) {
						try {
							ServiceAPI sp = new ServiceAPI(
									Constants.App42ApiKey,
									Constants.App42ApiSecret);
							String userName = Constants.GameName + userID;
							PushNotificationService push = sp
									.buildPushNotificationService();
							push.storeDeviceToken(userName, deviceId);
						} catch (Exception e) {
						}
						return null;
					}

					@Override
					protected void onPostExecute(Void result) {
						mRegisterTask = null;

					}

				};
				mRegisterTask.execute(null, null, null);
			}
		}
```


__Create Game:__ While starting a new game with opponent you have to create a game.
 Game creation has been done in in AsyncApp42ServiceApi.java
```
                      final JSONObject gameObject = new JSONObject();
					gameObject.put(Constants.GameFirstUserKey, uname1);
					gameObject.put(Constants.GameSecondUserKey, remoteUserName);
					gameObject.put(Constants.GameStateKey,
							Constants.GameStateIdle);
					gameObject.put(Constants.GameBoardKey,
							Constants.GameIdleState);
					gameObject.put(Constants.GameWinnerKey, "");
					gameObject.put(Constants.GameNextMoveKey, uname1);
					gameObject.put(Constants.GameIdKey, java.util.UUID
							.randomUUID().toString());

					// Insert in to user1's game collection
					storageService.insertJSONDocument(Constants.App42DBName,
							Constants.App42UserGamesCollectionPrefix + uname1,
							gameObject.toString());
					// Insert in to user2's game collection
					storageService.insertJSONDocument(Constants.App42DBName,
							Constants.App42UserGamesCollectionPrefix
									+ remoteUserName, gameObject.toString());
```

__Update Game:__ While playing game.
 Game updation has been done in in AsyncApp42ServiceApi.java
```
                      final JSONObject gameObject = new JSONObject();
					gameObject.put(Constants.GameFirstUserKey, uname1);
					gameObject.put(Constants.GameSecondUserKey, remoteUserName);
					gameObject.put(Constants.GameStateKey,
							Constants.GameStateIdle);
					gameObject.put(Constants.GameBoardKey,
							Constants.GameIdleState);
					gameObject.put(Constants.GameWinnerKey, "");
					gameObject.put(Constants.GameNextMoveKey, uname1);
					gameObject.put(Constants.GameIdKey, java.util.UUID
							.randomUUID().toString());

					// Insert in to user1's game collection
					storageService.insertJSONDocument(Constants.App42DBName,
							Constants.App42UserGamesCollectionPrefix + uname1,
							gameObject.toString());
					// Insert in to user2's game collection
					storageService.insertJSONDocument(Constants.App42DBName,
							Constants.App42UserGamesCollectionPrefix
									+ remoteUserName, gameObject.toString());
```

__Push meassge:__ Once you have played your turn , you have to send notification to your opponent
 Push message has been sent in in AsyncApp42ServiceApi.java

```
            	pushService.sendPushMessageToUser(Constants.GameName
							+ userName, newGameObj.toString());
```
