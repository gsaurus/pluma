# Pluma - Plug-in Management Framework

Pluma is an open source C++ framework for plug-in management.
Lightweight and designed for simplicity.

More information at:
http://pluma-framework.sourceforge.net


## Example

A host application define a Device interface, and use DeviceProviders (factory pattern) to create objects of type Device.
A certain plugin defines a Keyboard, witch is a Device.

Device hpp (shared):
```
 #include <Pluma/Pluma.hpp>
 class Device{
 public:
     virtual std::string getDescription() const = 0;
 };
 // create DevicedProvider class
 PLUMA_PROVIDER_HEADER(Device);
```
Device cpp (shared):
```
 #include "Device.hpp"
 generate DevicedProvider with version 6, and compatible with at least v.3
 PLUMA_PROVIDER_SOURCE(Device, 6, 3);
```

Keyboard code on the plugin side:
```
 #include <Pluma/Pluma.hpp>
 #include "Device.hpp"

 class Keyboard: public Device{
 public:
     std::string getDescription() const{
         return "keyboard";
     }
 };

 // create KeyboardProvider, it implements DeviceProvider
 PLUMA_INHERIT_PROVIDER(Keyboard, Device);
```
plugin connector:
```
 #include <Pluma/Connector.hpp>
 #include "Keyboard.hpp"

 PLUMA_CONNECTOR
 bool connect(pluma::Host& host){
     // add a keyboard provider to host
     host.add( new KeyboardProvider() );
     return true;
 }
```
Host application code:
```
 #include <Pluma/Pluma.hpp>

 #include "Device.hpp"
 #include <iostream>
 #include <vector>

 int main(){

     pluma::Pluma plugins;
     // Tell plugins manager to accept providers of the type DeviceProvider
     plugins.acceptProviderType<DeviceProvider>();
     // Load library "standard_devices" from folder "plugins"
     plugins.load("plugins", "standard_devices");

     // Get device providers into a vector
     std::vector<DeviceProvider*> providers;
     plugins.getProviders(providers);

     // create a Device from the first provider
     if (!providers.empty()){
         Device* myDevice = providers.first()->create();
         // do something with myDevice
         std::cout << device->getDescription() << std::endl;
         // and delete it in the end
         delete myDevice;
     }
     return 0;
 }
```

