# MavLink.NET

A better MAVLink object generation for C#. Richer message classes are generated from the object definitions.

## What is MAVLink

MAVLink is a very lightweight, header-only message marshalling library for micro air vehicles. It is used by several autopilots and ground station software. More details can be found on the homepage <http://qgroundcontrol.org/mavlink/start>.

## Why is it needed?

MAVLink already provides a generator for C# classes. So, why another implementation? Two main reasons: 

* The code generated by the standard MAVLink generator doesn't adhere to the .NET naming conventions. They are very C style. 
  * Classes are lowercase/underscore style (p.e. Msg_mount_status). A more Pascal naming style would be desired (i.e. MsgMountStatus).
  * Enums are all uppercase, enum name repeated in the enum members (p.e. MAV_MOUNT_MODE.MAV_MOUNT_MODE_RETRACT)
* A lot of the semantics of the protocol are lost in the generation: enumeration fields are not generated as enumerations, but instead as ints or bytes. Again, this is fine in C, but the code in C# becomes unnecesarily messy. Also, parameters look like an add-on extension that is not easily followed in the code either. Better message metadata can be generated. 

Here is a sample of code that uses the original MAVLink generator:

        Msg_heartbeat msg = new Msg_heartbeat()
        {
            type = (byte)MAV_TYPE.MAV_TYPE_HELICOPTER,
            autopilot = (byte)MAV_AUTOPILOT.MAV_AUTOPILOT_ARDUPILOTMEGA,
            base_mode = (byte)(MAV_MODE_FLAG.MAV_MODE_FLAG_GUIDED_ENABLED | MAV_MODE_FLAG.MAV_MODE_FLAG_SAFETY_ARMED)
        };

Of course, nothing prevents you from assigning an enum field a value from the wrong enumeration: 

            type = (byte)MAV_MODE_FLAG.MAV_MODE_FLAG_TEST_ENABLED 

Now, here is how the same code looks with the MavLink.NET classes: 

        UasHeartbeat msg = new UasHeartbeat()
        {
            Type = MavType.Helicopter,
            Autopilot = MavAutopilot.Ardupilotmega,
            BaseMode = MavModeFlag.GuidedEnabled | MavModeFlag.SafetyArmed
        };

With the added benefit of safe types and code completion. 

MavLink.NET tries to solve those problems, at the expense of **not being API compatible** with the original MAVLink generated classes. This is intended, as a way to make the code that uses the protocol easier to read. If this isn't a reason good enough for you, that's fine, go ahead and use the generated classes from the python Mavlink generator. These classes are intended for new code more than changing existing code. 

# Usage

* mavlinkobjectgenerator is the library containing the generator. 
* mavlinkgen is the executable that generates the classes. 
* mavlink.net is the library that holds the generated code and the protocol parsing logic. 

To generate the classes, first download the Mavlink definitions from <https://github.com/mavlink/mavlink>. Build the solution and run mavlinkgen like this (choose the XML definition file that you prefer):

    mavlinkgen --output="mavlink.net\messages.cs" c:\path to the mavlink repo\message_definitions\v1.0\ardupilotmega.xml
    

# License

This software is licensed under the MIT license (<http://opensource.org/licenses/MIT>).