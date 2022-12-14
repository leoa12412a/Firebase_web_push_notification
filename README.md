# 在PHP上Firebase推播

### step1
先取得firebase後台=>專案設定=>服務帳戶=>firebase admin sdk，點選"產生新的私密金鑰"

### step2
於專案下安裝composer
```
composer require google/apiclient:^2.10
```

### step3
```
public function test()
{
		putenv('GOOGLE_APPLICATION_CREDENTIALS=/home/keyfile.json');   //私鑰絕對路徑
		$scope = 'https://www.googleapis.com/auth/cloud-platform';  //固定
		$client = new \Google\Client();
		$client->setScopes($scope);
		$client->useApplicationDefaultCredentials();
		$access_token = $client->fetchAccessTokenWithAssertion()['access_token'];
		$access_token = str_replace("..", "", $access_token);
		
		$data = array(
			"message" => array(
				"notification" => array(
					"title" => "test",
					"body" => "This is an FCM Message",
				),
				"token" => "{$USER_TOKEN}",  //行動裝置token  
			),
		);
		
		$ch = curl_init();
		curl_setopt($ch, CURLOPT_URL, 'https://fcm.googleapis.com/v1/projects/ishine-a2f09/messages:send');
		curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type:application/json','Authorization: Bearer '.$access_token));
		
		if(!empty($data))
		{
			curl_setopt($ch, CURLOPT_POST, true);
			curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($data)); 
		}
		
		curl_setopt($ch, CURLOPT_RETURNTRANSFER,true);
	
		$output = curl_exec($ch); 
		curl_close($ch);
		
		print_R ($output);exit;
}
```
