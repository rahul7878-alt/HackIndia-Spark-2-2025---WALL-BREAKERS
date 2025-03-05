<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Flight Route Search</title>
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
  <link rel="stylesheet" href="https://unpkg.com/leaflet-control-geocoder/dist/Control.Geocoder.css" />
  <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@400;700&family=Roboto:wght@300;400;500&display=swap" rel="stylesheet">
  <style>
    body, html {
      margin: 0;
      padding: 0;
      width: 100%;
      height: 100%;
      font-family: 'Roboto', sans-serif;
    }

    #map {
      width: 100%;
      height: 70%;
    }

    #search-box {
      background: white;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
      margin: 10px auto;
      width: 80%;
      max-width: 800px;
      text-align: center;
    }

    #search-box input, #search-box select {
      padding: 8px;
      margin: 5px;
      width: 150px;
      border: 1px solid #ccc;
      border-radius: 5px;
    }

    #search-box button {
      padding: 8px 20px;
      background: #003366;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }

    #search-box button:hover {
      background: #002244;
    }

    #details-box {
      background: #003366;
      color: white;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
      margin: 10px auto;
      width: 80%;
      max-width: 800px;
      font-family: 'Montserrat', sans-serif;
    }

    #details-box h3 {
      margin-top: 0;
      font-size: 24px;
      font-weight: 700;
      color: #ffcc00;
      text-transform: uppercase;
    }

    #details-box p {
      margin: 10px 0;
      font-size: 16px;
      font-weight: 400;
      line-height: 1.6;
    }

    #details-box strong {
      color: #ffcc00;
      font-weight: 500;
    }

    .leaflet-control-geocoder {
      margin-top: 10px;
      margin-left: 10px;
    }
  </style>
</head>
<body>
  <div id="map"></div>
  <div id="search-box">
    <input type="text" id="from-country" placeholder="From Country" />
    <input type="text" id="to-country" placeholder="To Country" />
    <input type="number" id="max-price" placeholder="Max Price ($)" />
    <select id="duration">
      <option value="">Any Duration</option>
      <option value="short">Short (< 10h)</option>
      <option value="medium">Medium (10h - 20h)</option>
      <option value="long">Long (> 20h)</option>
    </select>
    <select id="timings">
      <option value="">Any Timing</option>
      <option value="morning">Morning</option>
      <option value="afternoon">Afternoon</option>
      <option value="evening">Evening</option>
      <option value="night">Night</option>
    </select>
    <button id="search-button">Search Flights</button>
  </div>
  <div id="details-box">
    <h3>Flight Information</h3>
    <p>Search for flights to see details.</p>
  </div>

  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
  <script src="https://unpkg.com/leaflet-control-geocoder/dist/Control.Geocoder.js"></script>
  <script>
    // Initialize the map
    const map = L.map('map').setView([20, 0], 2);

    // Add OpenStreetMap tiles
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: '© OpenStreetMap contributors',
    }).addTo(map);

    // Add the geocoder control
    L.Control.geocoder({
      defaultMarkGeocode: false,
    })
      .on('markgeocode', (e) => {
        const { center, name } = e.geocode;
        map.setView(center, 10);
        L.marker(center).addTo(map).bindPopup(name).openPopup();
      })
      .addTo(map);

    // Enhanced mock dataset of flight routes (40 countries)
    const flightRoutes = [
      {
        from: "Australia",
        to: "United States",
        takeoffAirport: "Melbourne Airport (MEL)",
        landingAirport: "Los Angeles Airport (LAX)",
        route: [
          [-37.6733, 144.8433], // Melbourne Airport
          [34.0522, -118.2437], // Los Angeles Airport
        ],
        duration: "15h 30m",
        distance: 12800,
        price: 1200,
        fuelRefills: 1,
        timings: "morning",
        takeoffTime: "11:30 AM",
        flightsPerDay: 3,
        flightTimes: ["6:00 AM", "11:30 AM", "3:00 PM"],
      },
      {
        from: "Australia",
        to: "India",
        takeoffAirport: "Melbourne Airport (MEL)",
        landingAirport: "Delhi Airport (DEL)",
        route: [
          [-37.6733, 144.8433], // Melbourne Airport
          [28.6139, 77.2090],   // Delhi Airport
        ],
        duration: "12h 45m",
        distance: 10500,
        price: 900,
        fuelRefills: 0,
        timings: "afternoon",
        takeoffTime: "6:30 PM",
        flightsPerDay: 2,
        flightTimes: ["12:00 PM", "6:30 PM"],
      },
      {
        from: "United States",
        to: "India",
        takeoffAirport: "New York Airport (JFK)",
        landingAirport: "Delhi Airport (DEL)",
        route: [
          [40.7128, -74.0060],  // New York Airport
          [28.6139, 77.2090],   // Delhi Airport
        ],
        duration: "18h 15m",
        distance: 11700,
        price: 1500,
        fuelRefills: 1,
        timings: "evening",
        takeoffTime: "8:00 PM",
        flightsPerDay: 4,
        flightTimes: ["6:00 PM", "8:00 PM", "10:00 PM", "11:30 PM"],
      },
      {
        from: "United Kingdom",
        to: "France",
        takeoffAirport: "London Heathrow Airport (LHR)",
        landingAirport: "Charles de Gaulle Airport (CDG)",
        route: [
          [51.4700, -0.4543],   // London Heathrow Airport
          [49.0097, 2.5479],    // Charles de Gaulle Airport
        ],
        duration: "1h 15m",
        distance: 344,
        price: 150,
        fuelRefills: 0,
        timings: "morning",
        takeoffTime: "7:00 AM",
        flightsPerDay: 10,
        flightTimes: ["6:00 AM", "7:00 AM", "8:00 AM", "9:00 AM", "10:00 AM", "11:00 AM", "12:00 PM", "1:00 PM", "2:00 PM", "3:00 PM"],
      },
      {
        from: "Germany",
        to: "Spain",
        takeoffAirport: "Frankfurt Airport (FRA)",
        landingAirport: "Madrid Barajas Airport (MAD)",
        route: [
          [50.0333, 8.5706],    // Frankfurt Airport
          [40.4719, -3.5626],   // Madrid Barajas Airport
        ],
        duration: "2h 30m",
        distance: 1300,
        price: 200,
        fuelRefills: 0,
        timings: "afternoon",
        takeoffTime: "1:00 PM",
        flightsPerDay: 5,
        flightTimes: ["10:00 AM", "1:00 PM", "4:00 PM", "7:00 PM", "10:00 PM"],
      },
      // Add more flight routes here...
    ];

    // Function to clear all existing polylines
    function clearFlightRoutes() {
      map.eachLayer((layer) => {
        if (layer instanceof L.Polyline) {
          map.removeLayer(layer);
        }
      });
    }

    // Function to draw a flight route
    function drawFlightRoute(route, flight) {
      const polyline = L.polyline(route, {
        color: 'blue',
        weight: 3,
        opacity: 0.7,
      }).addTo(map);

      polyline.bindPopup(`
        <b>${flight.from} to ${flight.to}</b><br>
        <strong>Takeoff Airport:</strong> ${flight.takeoffAirport}<br>
        <strong>Landing Airport:</strong> ${flight.landingAirport}<br>
        <strong>Duration:</strong> ${flight.duration}<br>
        <strong>Distance:</strong> ${flight.distance} km<br>
        <strong>Price:</strong> $${flight.price}<br>
        <strong>Fuel Refills:</strong> ${flight.fuelRefills}<br>
        <strong>Timings:</strong> ${flight.timings}<br>
        <strong>Takeoff Time:</strong> ${flight.takeoffTime}<br>
        <strong>Flights Per Day:</strong> ${flight.flightsPerDay}<br>
        <strong>Flight Times:</strong> ${flight.flightTimes.join(", ")}
      `);

      polyline.on('click', () => {
        updateDetailsBox(flight);
      });

      map.fitBounds(polyline.getBounds());
    }

    // Function to update the details box with flight information
    function updateDetailsBox(flight) {
      const detailsBox = document.getElementById('details-box');
      detailsBox.innerHTML = `
        <h3>Flight Information</h3>
        <p><strong>From:</strong> ${flight.from}</p>
        <p><strong>To:</strong> ${flight.to}</p>
        <p><strong>Takeoff Airport:</strong> ${flight.takeoffAirport}</p>
        <p><strong>Landing Airport:</strong> ${flight.landingAirport}</p>
        <p><strong>Duration:</strong> ${flight.duration}</p>
        <p><strong>Distance:</strong> ${flight.distance} km</p>
        <p><strong>Price:</strong> $${flight.price}</p>
        <p><strong>Fuel Refills:</strong> ${flight.fuelRefills}</p>
        <p><strong>Timings:</strong> ${flight.timings}</p>
        <p><strong>Takeoff Time:</strong> ${flight.takeoffTime}</p>
        <p><strong>Flights Per Day:</strong> ${flight.flightsPerDay}</p>
        <p><strong>Flight Times:</strong> ${flight.flightTimes.join(", ")}</p>
      `;
    }

    // Function to search and display flight routes
    document.getElementById('search-button').addEventListener('click', () => {
      const fromCountry = document.getElementById('from-country').value.trim();
      const toCountry = document.getElementById('to-country').value.trim();
      const maxPrice = parseFloat(document.getElementById('max-price').value);
      const duration = document.getElementById('duration').value;
      const timings = document.getElementById('timings').value;

      // Clear existing flight routes
      clearFlightRoutes();

      // Find matching flights
      const matchingFlights = flightRoutes.filter((flight) => {
        const matchesFrom = !fromCountry || flight.from.toLowerCase().includes(fromCountry.toLowerCase());
        const matchesTo = !toCountry || flight.to.toLowerCase().includes(toCountry.toLowerCase());
        const matchesPrice = !maxPrice || flight.price <= maxPrice;

        // Parse duration (e.g., "15h 30m" -> 15.5)
        const flightDuration = parseFloat(flight.duration.replace('h', '').replace('m', '').trim());
        const matchesDuration =
          !duration ||
          (duration === "short" && flightDuration < 10) ||
          (duration === "medium" && flightDuration >= 10 && flightDuration <= 20) ||
          (duration === "long" && flightDuration > 20);

        const matchesTimings = !timings || flight.timings.toLowerCase() === timings.toLowerCase();

        return matchesFrom && matchesTo && matchesPrice && matchesDuration && matchesTimings;
      });

      if (matchingFlights.length > 0) {
        matchingFlights.forEach((flight) => {
          drawFlightRoute(flight.route, flight);
        });

        // Update the details box with the first matching flight
        updateDetailsBox(matchingFlights[0]);
      } else {
        alert("No flights found for the selected criteria.");
        const detailsBox = document.getElementById('details-box');
        detailsBox.innerHTML = `
          <h3>Flight Information</h3>
          <p>No flights found for the selected criteria.</p>
        `;
      }
    });
  </script>
</body>
</html>
