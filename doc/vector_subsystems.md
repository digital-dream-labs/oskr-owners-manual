# Vector subsystems

The Vector software consists of several interacting subsystems. The
internal codename for the project was **Victor** and we will see that
reflected in the naming with things like `vic-`.

As the software is open sourced there will be more detailed
descriptions of each component.

## vic-switchboard

This is the process that handles BLE communications and forwards them
to the rest of the system.

## vic-cloud

This is the subsystem that interacts with cloud services. Most
importantly this provides access to the voice recognition
functionality.

## vic-anim

This controls the animations that can be played on the robot. In this
case animations are a combination of motors moving and what the
display shows. If Vector is dancing and smiling all the work is
happening here.

## vic-gateway

The internet gateway which authenticates and routes SDK messages to
the vector.

## vic-engine

This is the most elaborate and powerful module by far. The engine
provides the high level functions for Vectors behavior, a complete AI
engine that controls his mood, and much much more.

## vic-dasmgr

This controls fleet logging via an ANKI developed system called DAS so
we can keep an eye on the health of all the robots out there in the wild.

