+++
date = 2022-08-10
title = "Compass App with Variable Heading"
description = "An Android app that drops a pin or searches a location and points you toward it with a live compass."
authors = ["Alyn Musselman"]
[taxonomies]
tags = ["Java", "Android SDK", "Google Maps API", "Google Places API"]
[extra]
math = true
image = "compass.png"
+++

## Motivation

When I was delivering for DoorDash I kept hitting the same problem: I'd be so
close to the delivery address that the map was useless. Even when I could see the
house numbers on Google Maps, actually *reaching* the door in a dense apartment
complex or a maze of identical buildings was hard once I'd lost my bearing. This
app was built to solve exactly that — instead of a map you have to interpret, it
gives you a single arrow that points straight at the destination.

The long-term vision was a physical "hand" mounted on the car's dash, linked to
the app, that would physically point the direction you needed to drive. That part
is still on the to-do list, but the software side — pick a destination, get a
live heading toward it — works.

## Methodology

Since I was developing on an Android phone, I built the app in **Android Studio
with Java** for the application logic and **XML** for the layouts. Three Google
services do the heavy lifting:

- **Google Maps API** — renders the interactive map and supplies the device's
  real-time location through a `FusedLocationProviderClient`.
- **Google Places API** — powers a search bar that turns a typed address into
  coordinates.
- The device's **orientation sensor** — drives the rotating compass graphic.

The interesting technical pieces were the navigation between screens (intents),
passing the chosen coordinates around, computing a heading from a pair of
coordinates, and rotating the on-screen pointer to match.

### Intents and passing the destination

An *intent* is the Android mechanism for moving from one screen to another (and,
optionally, handing data along with the jump). Here I needed an intent that takes
the user from the map screen back to the compass screen once a valid destination
has been chosen. The map screen makes its pin **draggable**, and when the user
drops it, the pin's coordinates are packed into the intent and sent to the main
activity:

```java
googleMap.setOnMarkerDragListener(new GoogleMap.OnMarkerDragListener() {
    @Override
    public void onMarkerDragEnd(@NonNull Marker marker) {
        Intent destination = new Intent(getApplicationContext(), MainActivity.class);
        String dest_coords = marker.getPosition().toString();   // "lat/lng(...)"
        destination.putExtra("coords", dest_coords);            // attach to intent
        startActivity(destination);                             // hand off to compass screen
    }
    // onMarkerDrag / onMarkerDragStart unused
});
```

Meanwhile the main screen continuously refreshes the user's own position from the
fused location provider, so both ends of the heading — *where you are* and *where
you're going* — are always available.

### Generating the heading

This is where I got to apply some fun math. Given the user's current position
$(\varphi_1, \lambda_1)$ and the destination $(\varphi_2, \lambda_2)$ (latitudes
$\varphi$, longitudes $\lambda$, in radians), the **initial great-circle bearing**
— the compass direction to set off in — is

$$
\theta = \operatorname{atan2}\!\Big(\sin(\Delta\lambda)\cos\varphi_2,\;
\cos\varphi_1 \sin\varphi_2 - \sin\varphi_1 \cos\varphi_2 \cos(\Delta\lambda)\Big),
\qquad \Delta\lambda = \lambda_2 - \lambda_1 .
$$

Because `atan2` returns a value in $(-\pi, \pi]$, the result is converted to
degrees and wrapped into a clean $0^\circ$–$360^\circ$ compass bearing:

$$
\theta_{\text{deg}} = \big(\operatorname{deg}(\theta) + 360\big) \bmod 360 .
$$

Subtracting the phone's own magnetic heading from this bearing gives the angle the
pointer needs to rotate to — so the arrow always points at the destination
regardless of which way the phone is facing.

### Rotating the pointer

The phone's heading comes from the orientation sensor. `MainActivity` implements
`SensorEventListener`, and every sensor update carries the azimuth (rotation about
the vertical *z*-axis) in `event.values[0]`. I feed that into a `RotateAnimation`
to spin the pointer image smoothly to the new angle:

```java
@Override
public void onSensorChanged(SensorEvent event) {
    float degree = Math.round(event.values[0]);     // azimuth, degrees
    tvHeading.setText("Heading: " + degree + " degrees");

    RotateAnimation ra = new RotateAnimation(
            currentDegree, -degree,                 // from -> to
            Animation.RELATIVE_TO_SELF, 0.5f,       // pivot about image center
            Animation.RELATIVE_TO_SELF, 0.5f);
    ra.setDuration(210);
    ra.setFillAfter(true);                          // hold final angle
    image.startAnimation(ra);
    currentDegree = -degree;
}
```

For the pointer graphic itself I forked a small compass tutorial and swapped in
this image of Mickey Mouse's hand:

![The Mickey Mouse hand used as the compass pointer](pointer_hand.png)

## Results

The working app does what it set out to do: open the map, drop a pin (or search an
address), hand the coordinates back to the main screen, and watch the hand rotate
in real time to point toward the destination as you turn. The core loop —
permissions, live location, intent hand-off, bearing math, and a smoothly animated
pointer — is all in place. What remains is the fun hardware extension: a
dash-mounted physical pointer that mirrors the on-screen hand, which is a project
for another day.
