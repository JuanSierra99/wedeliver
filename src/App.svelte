<script>
  import { onMount } from "svelte";
  import { initializeApp } from "firebase/app";
  import { getAnalytics } from "firebase/analytics";
  import { getFirestore } from "firebase/firestore";
  import { collection, getDocs } from "firebase/firestore";
  import mapboxgl from "mapbox-gl";
  import "mapbox-gl/dist/mapbox-gl.css";

  // Your web app's Firebase configuration
  // For Firebase JS SDK v7.20.0 and later, measurementId is optional
  //ADD YOUR FIREBASE CONFIG DATA HERE
  const firebaseConfig = {
    apiKey: "",
    authDomain: "",
    projectId: "",
    storageBucket: "",
    messagingSenderId: "",
    appId: "",
    measurementId: "",
  };

  // Initialize Firebase
  const app = initializeApp(firebaseConfig);
  // Initialize firebase analytics
  const analytics = getAnalytics(app);

  // Initialize Cloud Firestore and get a reference to the service
  const db = getFirestore(app);

  const initializeMap = async () => {
    // TO MAKE THE MAP APPEAR YOU MUST ADD YOUR ACCESS TOKEN FROM https://account.mapbox.com
    mapboxgl.accessToken = "YOUR_MAPBOX_API_KEY";
    const map = new mapboxgl.Map({
      container: "map", // container ID
      style: "mapbox://styles/mapbox/streets-v12", // style URL
      center: [-118, 34], // starting position [lng, lat]
      zoom: 11, // starting zoom
    });

    const querySnapshot = await getDocs(collection(db, "locations"));
    // Retrieve drivers from database, and make a map marker for each drivers location
    querySnapshot.forEach((doc) => {
      // doc.data() is never undefined for query doc snapshots
      let data = doc.data(); // driver object
      console.log(doc.id, " => ", doc.data());
      new mapboxgl.Marker({
        color: "#FF00FF",
        draggable: false,
      })
        .setLngLat([data.location.longitude, data.location.latitude]) // Use the drivers coordinates
        .addTo(map);
    });
  };

  // onMount will ensure that the <div id="map"> element exists before running the JavaScript code. This element is needed to display the map, and we will get an error if its not found.
  onMount(() => {
    initializeMap();
  });
</script>

<main>
  <h1>We Deliver</h1>
  <div id="map"></div>
</main>

<style>
  #map {
    height: 500px; /* Adjust the height as needed */
    width: 800px; /* Adjust the width as needed */
  }
</style>
