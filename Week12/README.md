# Week 12: Geolocation & Maps Integration

## Overview

This week, we will master geolocation and map features in React Native. You'll learn to access the device's GPS, track user location in real-time, display interactive maps with markers and routes, and build location-based features. These skills are essential for delivery apps, travel apps, social check-ins, and any location-aware mobile application. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 1: Geolocation Basics & Permissions
*   **Theory:** Read about [expo-location](https://docs.expo.dev/versions/latest/sdk/location/) for accessing device GPS. Understand iOS vs Android location permissions.
*   **Practice:**
    - Install: `npx expo install expo-location`
    - Request location permissions: `Location.requestForegroundPermissionsAsync()`
    - Understand permission types: foreground, background, precise vs approximate
    - Get current position once: `Location.getCurrentPositionAsync()`
    - Extract latitude, longitude, accuracy, altitude from position object
    - Handle permission denial with user-friendly messages
    - Check if location services are enabled: `Location.hasServicesEnabledAsync()`
    - Implement error handling for location unavailable scenarios

### Day 2: Real-Time Location Tracking & Geofencing
*   **Theory:** Learn about `watchPositionAsync` for continuous location updates and geofencing concepts.
*   **Practice:**
    - Implement real-time location tracking with `Location.watchPositionAsync()`
    - Configure accuracy: `LocationAccuracy.High`, `Balanced`, `Low`
    - Set distance filter: only update when user moves X meters
    - Display user's current coordinates in real-time
    - Calculate distance between two coordinates using haversine formula
    - Implement basic geofencing: trigger event when user enters/exits a radius
    - Understand battery implications of continuous location tracking
    - Clean up location watchers when component unmounts

### Day 3: Integrating React Native Maps
*   **Theory:** Study [react-native-maps](https://github.com/react-native-maps/react-native-maps) documentation. Understand MapView, Markers, and Polylines.
*   **Practice:**
    - Install: `npm install react-native-maps` (use `expo install react-native-maps` for Expo)
    - Create a basic MapView component
    - Set initial region with latitude, longitude, latitudeDelta, longitudeDelta
    - Center map on user's current location
    - Add a Marker at specific coordinates
    - Customize marker icons (use custom images or emojis)
    - Handle marker press events with `onPress`
    - Implement map press to add new markers: `onPress={(e) => e.nativeEvent.coordinate}`

### Day 4: Advanced Map Features - Polylines, Polygons, Callouts
*   **Theory:** Learn about drawing routes, regions, and custom callouts on maps.
*   **Practice:**
    - Draw a Polyline (route) between multiple coordinates
    - Style Polylines: color, width, dash pattern
    - Create Polygons for regions (e.g., delivery zones, neighborhoods)
    - Implement custom Marker callouts (info windows that appear on tap)
    - Add buttons inside callouts (e.g., "Get Directions", "View Details")
    - Animate camera to specific coordinates with `mapRef.animateToRegion()`
    - Fit map to show all markers using `fitToCoordinates()`
    - Cluster markers when there are many close together (use `react-native-maps-super-cluster`)

### Day 5: Directions & Route Planning
*   **Theory:** Learn to integrate with mapping APIs (Google Directions API, Mapbox Directions) to get routes between locations.
*   **Practice:**
    - Set up Google Maps Directions API or use a directions service
    - Fetch route between two points (origin and destination)
    - Parse route response to get polyline coordinates
    - Display route on map using Polyline
    - Show estimated distance and duration
    - Implement "Get Directions" feature that opens native maps app
    - Use `Linking.openURL()` with geo URLs for Apple Maps or Google Maps
    - Calculate ETA based on current location and destination

### Day 6: Project Application - Delivery Tracker App
*   **Task:** Build a delivery/ride-sharing style app with live location tracking:
    - **Map Screen (Main):**
      - Full-screen MapView showing user's current location
      - Blue dot marker for user's real-time position
      - Track user location continuously (updates every 10 meters or 5 seconds)
      - Multiple delivery location markers on the map
      - Tapping a marker shows delivery details in a callout
    - **Active Delivery View:**
      - When user selects a delivery, show:
        - Route from user's location to delivery destination (Polyline)
        - ETA and distance
        - "Start Delivery" button
      - Simulate driver movement along the route (for demo purposes)
      - Update polyline as user moves
      - Show arrival notification when user reaches destination (geofence trigger)
    - **Delivery List Screen:**
      - List of pending deliveries (FlatList)
      - Each item shows: address, customer name, distance from user
      - Sort by distance (closest first)
      - Tap to center map on delivery location
    - **Features:**
      - Permission handling for location access
      - Show "Enable Location" prompt if permissions denied
      - Battery-efficient location tracking (balanced accuracy)
      - Background location updates (explain foreground vs background permissions)
      - "My Location" button to re-center map on user
      - Search places functionality (use geocoding API)
    - **Bonus:**
      - Toggle between map types: standard, satellite, hybrid
      - Show traffic layer
      - Implement place autocomplete for searching destinations
    - **Goal:** Build a production-quality location-based app with real-time tracking

### Day 7: Review and Refactor
*   **Task:**
    - Review all geolocation and maps code from the week
    - Test the delivery tracker app on a physical device (location simulators are limited)
    - Optimize location tracking: balance accuracy vs battery consumption
    - Refactor: create reusable components (MapWithMarkers, RoutePolyline, LocationPermissionGuard)
    - Create custom hooks: `useCurrentLocation()`, `useLocationTracking()`, `useDirections()`
    - Test edge cases: what if GPS is unavailable? What if user moves to area with poor GPS signal?
    - Implement location caching: remember last known location
    - Document location tracking architecture and permission flows
    - Research background location tracking regulations (iOS App Store, Android Play Store requirements)

## Key Takeaways

By the end of this week, you should:
- ✅ Master geolocation APIs and permission handling
- ✅ Implement real-time location tracking with battery efficiency
- ✅ Integrate and customize React Native Maps
- ✅ Display markers, routes, and regions on maps
- ✅ Fetch and display directions between locations
- ✅ Build location-based apps like delivery trackers, ride-sharing, social check-ins

## Resources

- [Expo Location API](https://docs.expo.dev/versions/latest/sdk/location/)
- [React Native Maps Documentation](https://github.com/react-native-maps/react-native-maps)
- [Google Maps Directions API](https://developers.google.com/maps/documentation/directions/overview)
- [Mapbox Directions API](https://docs.mapbox.com/api/navigation/directions/)
- [Geofencing Guide](https://docs.expo.dev/versions/latest/sdk/location/#geofencing)

## Looking Ahead to Week 13

Next week, we'll dive into touch gestures and basic animations. You'll learn the Gesture Handler library for complex touch interactions (swipe, pinch, pan) and start exploring the Animated API for creating smooth, performant animations.
