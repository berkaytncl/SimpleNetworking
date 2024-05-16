# SimpleNetworking

SimpleNetworking is a native, networking library written in Swift.

## Setup

<details>
<summary> Details (Click to expand) </summary>
<br>
Add the package to your Xcode project.

```
https://github.com/berkaytncl/SimpleNetworking.git
```

Import the package

```swift
import SimpleNetworking
```

This code performs a network request, processes the incoming data, and ensures UI updates are done appropriately on the main thread. Additionally, it provides a modern and efficient way to handle asynchronous data flow using Combine.

```swift
guard let url = URL(string: "https://api.openweathermap.org/data/2.5/weather?lat=\(coord.lat)&lon=\(coord.lon)&appid=\(apiKey)") else { return }
        
NetworkingManager.download(url: url)
    .decode(type: WeatherData.self, decoder: JSONDecoder())
    .receive(on: DispatchQueue.main)
    .sink(receiveCompletion: NetworkingManager.handleCompletion,
          receiveValue: { [weak self] returnedWeatherData in
        guard let self else { return }
        self.currentLocationWeatherData = returnedWeatherData
    })
    .store(in: &cancellables)
```

Provide the url to send network request.

```swift
guard let url = URL(string: "https://api.openweathermap.org/data/2.5/weather?lat=\(coord.lat)&lon=\(coord.lon)&appid=\(apiKey)") else { return }
        
NetworkingManager.download(url: url)
```

The downloaded data is decoded from JSON into the WeatherData type using JSONDecoder.

```swift
    .decode(type: WeatherData.self, decoder: JSONDecoder())
```

It ensures that future values in the Combine stream are received on the main thread. Since UI updates typically need to occur on the main thread, this function ensures proper handling of UI updates.

```swift
    .receive(on: DispatchQueue.main)
```

It subscribes to the Combine stream. The receiveCompletion part handles the completion status of the stream, while the receiveValue part processes each incoming value. 

```swift
    .sink(receiveCompletion: NetworkingManager.handleCompletion,
          receiveValue: { [weak self] returnedWeatherData in
        guard let self else { return }
        self.currentLocationWeatherData = returnedWeatherData
    })
```

It adds the subscription to a collection named cancellables. This is used to cancel tracking of the Combine stream, particularly important for preventing memory leaks when the subscription ends.

```swift
    .store(in: &cancellables)
```
</details>
