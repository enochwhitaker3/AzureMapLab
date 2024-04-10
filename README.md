# Integrating Azure Map in Blazor Web App

Made by Enoch Whitaker
## Step One

### Create Azure Map Service

* Sign into Azure
* Click on “Create a resource” and search for “Azure Maps.”
* Select “Azure Maps” and click on “Create.”
* Fill in the required details and click “Review + Create.”
* Once the deployment is complete, go to the resource and copy the primary key for later use.

## Step Two

### Create New Blazor Web App

* Head into Visual Studios and create a dotnet 8 Blazor Web App

## Step Three

### Add the Azure Maps Key into User Secrets

* Add the following lines into you Projects User Secrets to securly store the key 
```csharp
{
  "MapKey": "YOUR_KEY_GOES_HERE"
}
```

## Step Four

### Add the Azure Maps SDK to App.Razor

* Add the following lines into you App.razor at the bottom of the head section
```csharp
    <script src="map.js"></script>
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/3/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/3/atlas.min.js"></script>
```

## Step Five 

### Turn on InteractiveServer mode

* Change the Routes to being the line below
```csharp
    <Routes @rendermode="InteractiveServer" />
```

## Step Six

### Create Javascript file named [map.js] for map initialization

* Create in the root of wwwroot folder, then place the following code inside of the javascript file
```csharp
var map;

function initializeMap(mapsKey) {
    // Initialize the Azure Maps
    atlas.setSubscriptionKey(mapsKey);
    // Create the map instance
    map = new atlas.Map("mapDiv", {
        view: "Auto",
        center: [-122.129, 47.640],
        zoom: 3
    });

    navigator.geolocation.getCurrentPosition(function (position) {
        var userLocation = [position.coords.longitude, position.coords.latitude];
        map.setCamera({
            center: userLocation,
            zoom: 13
        });
    });

    var marker = new atlas.HtmlMarker({
        color: 'DodgerBlue',
        text: 'O',
        position: [- 111.583795, 39.360108],
        popup: new atlas.Popup({
            content: `<div style="padding:10px">You Are Here =)</div>`,
            pixelOffset: [0, -30]
        })
    });
    map.markers.add(marker);

    map.events.add('click', marker, () => {
        marker.togglePopup();
    });
}


```


## Step Seven

### Create a new Component to call the javascript

* Create in the root of Components folder, then place the following code inside of the razor component
```csharp
@inject IJSRuntime JSRuntime

<div id="mapDiv" style="width:100%;height:400px;"></div>
@code {
    [Parameter] public string MapsKey { get; set; }
    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync("initializeMap", MapsKey);
        }
    }
}
```

## Step Eight

### Create a new component to display the newly made Map

* Create in the Components/layout folder, and use the code below
```csharp
@page "/map"
@inject IConfiguration config

<h3>MapPage</h3>
<Map MapsKey="@MapsKey"></Map>

@code {
    private string? MapsKey { get; set; }

    protected override void OnInitialized()
    {
        MapsKey = config.GetValue<string>("MapKey") ?? throw new Exception("Map key is not in the secrets");
    }

}
```

## Step Nine

### Run your project and head to localhost/map to see map!


