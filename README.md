A simple to use, Riot Games API wrapper for Java.
This library makes it easy to gather and use League of Legends data in your web and mobile (Android) apps.

## Disclaimer
This product is not endorsed, certified or otherwise approved in any way by Riot Games, Inc. or any of its affiliates.

## Requirements

**riot-api-java** requires the following libraries:
- [Google Gson](https://code.google.com/p/google-gson/)

## Download
[Download (Version 3.4.1)](https://www.dropbox.com/s/te7kxqqrhzsp60e/riot-api-java.jar?dl=0) 

## Setup

[Download](https://www.dropbox.com/s/te7kxqqrhzsp60e/riot-api-java.jar?dl=0) the .jar file, and add it as an external library to your project.

If you are using Eclipse, this can be done by right clicking your project, and selecting:

Build Path -> Configure Build Path -> Libraries -> Add External Jars

and selecting the jar under the Order and Export tab.

## Usage

This library can be used strictly according to the [Riot API Documentation](https://developer.riotgames.com/api/methods) like so:

```java
import java.util.Map;
import constant.Region;
import dto.Summoner.Summoner;
import main.java.riotapi.RiotApi;
import com.google.gson*;

public class Example {

	public static void main(String[] args) {
		
		RiotApi api = new RiotApi("YOUR-API-KEY-HERE");

		Map<String, Summoner> summoners = api.getSummonersByName(Region.NA, "rithms, tryndamere");
		Summoner summoner = summoners.get("rithms");
		long id = summoner.getId();
		System.out.println(id);
	}

}

```


Available accessors allow you to accomplish similar tasks in a different way.
Below is an example of how to set your region. Because the region was set before a method was called, there is no need to pass in the region parameter. This is great for people that know they will only be working in one region when making multiple requests. The same can be done for the season.


```java
import java.util.Map;
import constant.Region;
import dto.Summoner.Summoner;
import main.java.riotapi.RiotApi;
import com.google.gson*;

public class Example {

	public static void main(String[] args) {
		
		RiotApi api = new RiotApi("YOUR-API-KEY-HERE");
		
		api.setRegion(Region.NA);
		Map<String, Summoner> summoners = api.getSummonersByName("rithms, tryndamere");
		Summoner summoner = summoners.get("rithms");
		long id = summoner.getId();
		System.out.println(id);
	}

}

```


It is important to be aware of your personal rate limit. Any method call from the RiotAPI is a request that counts towards your rate limit, with exceptions to the accessors/mutators of region, key, and season, as well as any requests regarding static data. The below code makes 2 requests; one request for a summoner, and another for ranked stats of a summoner.



```java
import constant.Region;
import constant.Season;
import dto.Stats.RankedStats;
import main.java.riotapi.RiotApi;
import com.google.gson*;

public class Example {

	public static void main(String[] args) {
		
		RiotApi api = new RiotApi("YOUR-API-KEY-HERE", Region.NA);
		api.setSeason(Season.FIVE);
		
		RankedStats rankedStats = api.getRankedStats(api.getSummonersByName("rithms, tryndamere").get("rithms").getId());
	}

}

```

## Android Example

When using this library for Android development, you must be aware that all API calls are done over the network. Thus,
every api call must be done off the main/UI thread. To do so, you can make use of [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html).

Here is a simple example making an API call using AsyncTask:

```java
public class MainActivity extends Activity{
 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
 
        final Button button = (Button) findViewById(R.id.button1);
        button.setOnClickListener(new View.OnClickListener() {
            public void onClick(View v) {
                FetchSummonerTask summonerTask = new FetchSummonerTask();
                summonerTask.execute("summoner name");
            }
        });
 
    }
 
    public class FetchSummonerTask extends AsyncTask<String, Void, Summoner> {
 
        @Override
        protected Summoner doInBackground(String... params) {
 
            try {
                Summoner summoner = ((GlobalClass)getActivity().getApplication()).getApi()
                        .getSummonerByName(params[0])
                        .get(params[0].replaceAll("\\s+", "").toLowerCase());
                if(summoner != null) {
                    return summoner;
                }
            } catch (RiotApiException e) {
                e.printStackTrace();
            }
 
            return null;
        }
 
        @Override
        protected void onPostExecute(Summoner result) {
            super.onPostExecute(result);
            if(result != null) {
                TextView dis = (TextView) findViewById(R.id.textView1);
                dis.setText(String.valueOf((result.getSummonerLevel())));
            }
        }
    }
}

```

Note this example makes use of a global class containing the RiotApi instance:

```java
import android.app.Application;
import main.java.riotapi.RiotApi;
 
public class GlobalClass extends Application {
 
    private RiotApi api;
 
    public GlobalClass() {
        this.api = new RiotApi("API-KEY");
    }
 
    public RiotApi getApi() {
        return api;
    }
 
}
```

To read more about performing network operations in Android read [here.](http://developer.android.com/training/basics/network-ops/index.html)


## Documentation
The documentation for this library can be found [here.](http://rithms.im/riot-api-java/doc/)

## API Versions
The current version of this library supports the following Riot Games API versions:
- **champion-v1.2 [BR, EUNE, EUW, KR, LAN, LAS, NA, OCE, RU, TR]**
- **game-v1.3 [BR, EUNE, EUW, KR, LAN, LAS, NA, OCE, RU, TR]**
- **league-v2.5 [BR, EUNE, EUW, KR, LAN, LAS, NA, OCE, RU, TR]**
- **lol-static-data-v1.2 [BR, EUNE, EUW, KR, LAN, LAS, NA, OCE, RU, TR]**
- **lol-status-v1.0 [BR, EUNE, EUW, LAN, LAS, NA, OCE, PBE, RU, TR]**
- **match-v2.2 [BR, EUNE, EUW, KR, LAN, LAS, NA, OCE, PBE, RU, TR]**
- **matchhistory-v2.2 [BR, EUNE, EUW, KR, LAN, LAS, NA, OCE, PBE, RU, TR]**
- **stats-v1.3 [BR, EUNE, EUW, KR, LAN, LAS, NA, OCE, RU, TR]**
- **summoner-v1.4 [BR, EUNE, EUW, KR, LAN, LAS, NA, OCE, RU, TR]**
- **team-v2.4 [BR, EUNE, EUW, KR, LAN, LAS, NA, OCE, RU, TR]**

## Contributing
All contributions are appreciated.
If you would like to contribute to this repo, please send a pull request.

## Credits/Contributors
- [FriesischScott](https://github.com/FriesischScott)
- [fKunstner](https://github.com/fKunstner)
- [Linnun] (https://github.com/Linnun)

## Discussion
The discussion thread for this library can be found [here](https://developer.riotgames.com/discussion/riot-games-api/show/VmGxpdN8).
Have a suggestion, complaint, or question? [Tweet me](http://twitter.com/itsRithms) for a quick reply, or send me an email (tcaldwel@nmsu.edu).
