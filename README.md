OBJECTIVES

* Identify traffic congestion, floods, major accidents, and road blockages.
* Find safer and faster alternative routes.
* Help ambulances and emergency vehicles reach their destinations quickly.
* Reduce travel delays during emergency situations.
* Provide useful route information to the public.

Key Features

* Smart Route Navigation: Suggests alternative routes when the usual route is blocked.
* Flood Detection: Identifies flood-affected roads using available data.
* Accident Alerts: Provides information about major accidents and road closures.
* Traffic Monitoring: Identifies congested roads using live traffic data.
* Emergency Route Planning: Helps emergency vehicles find suitable routes.
* Real-Time Updates: Updates route recommendations when new information becomes available.

Future Scope

* Integrate live satellite and flood-monitoring data.
* Use AI and Machine Learning to predict traffic congestion and possible road disruptions.
* Develop a mobile application for Android and iOS.
* Integrate GPS navigation and live traffic APIs.
* Send emergency alerts to hospitals and ambulance services.
* Expand the system to cover more cities and rural areas.

Technologies

* Frontend: HTML, CSS, JavaScript
* Backend: Python with Flask
* Maps and Navigation: Google Maps API or OpenStreetMap
* Database: Firebase or PostgreSQL
* AI/ML: Python and Machine Learning libraries
* Data Sources: Live traffic feeds, weather APIs, and available flood or satellite data

Project Status

Currently in the idea and planning stage. The system architecture, technology selection, and prototype development are planned as the next steps.









CODING

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Logistics Recovery</title>

    <style>
        * {
            box-sizing: border-box;
        }

        body {
            font-family: Arial, sans-serif;
            background: #f1f5f9;
            margin: 0;
            padding: 20px;
            color: #1e293b;
        }

        .container {
            max-width: 850px;
            margin: 20px auto;
        }

        h1, h2 {
            color: #1d4ed8;
        }

        .card {
            background: white;
            padding: 22px;
            margin-bottom: 20px;
            border-radius: 12px;
            box-shadow: 0 3px 12px #00000010;
        }

        label {
            display: block;
            margin: 12px 0 6px;
            font-weight: bold;
        }

        input, select {
            width: 100%;
            padding: 11px;
            border: 1px solid #cbd5e1;
            border-radius: 6px;
            font-size: 16px;
        }

        button {
            margin-top: 16px;
            padding: 12px 18px;
            background: #2563eb;
            color: white;
            border: none;
            border-radius: 6px;
            cursor: pointer;
            font-size: 15px;
        }

        button:hover {
            background: #1d4ed8;
        }

        .secondary {
            background: #475569;
            margin-left: 6px;
        }

        .vehicle {
            border: 1px solid #e2e8f0;
            padding: 14px;
            border-radius: 8px;
            margin-top: 12px;
            overflow-wrap: anywhere;
        }

        .message {
            margin-top: 12px;
            font-weight: bold;
        }

        .error {
            color: #b91c1c;
        }

        .success {
            color: #15803d;
        }
    </style>
</head>

<body>
<div class="container">

    <h1>AI Logistics Recovery System</h1>
    <p>Manually enter vehicle details and save them to the database.</p>

    <section class="card">
        <h2>Add Vehicle</h2>

        <form id="vehicleForm">
            <label for="vehicleId">Vehicle ID</label>
            <input
                type="text"
                id="vehicleId"
                placeholder="Example: TRK-205"
                required
            >

            <label for="status">Vehicle Status</label>
            <select id="status" required>
                <option value="AVAILABLE">AVAILABLE</option>
                <option value="DELAYED">DELAYED</option>
                <option value="IN_TRANSIT">IN TRANSIT</option>
                <option value="UNAVAILABLE">UNAVAILABLE</option>
            </select>

            <label for="latitude">Latitude</label>
            <input
                type="number"
                id="latitude"
                step="any"
                min="-90"
                max="90"
                placeholder="Example: 11.115"
                required
            >

            <label for="longitude">Longitude</label>
            <input
                type="number"
                id="longitude"
                step="any"
                min="-180"
                max="180"
                placeholder="Example: 77.045"
                required
            >

            <button type="submit">Add Vehicle</button>
            <button
                type="button"
                class="secondary"
                id="refreshButton"
            >
                Refresh Vehicles
            </button>
        </form>

        <div id="message" class="message" role="status"></div>
    </section>

    <section class="card">
        <h2>Saved Vehicles</h2>
        <div id="vehicleResults">Loading vehicles...</div>
    </section>

</div>

<script>
    const API_URL = "http://127.0.0.1:8000";

    const form = document.getElementById("vehicleForm");
    const message = document.getElementById("message");
    const results = document.getElementById("vehicleResults");

    function showMessage(text, isError = false) {
        message.textContent = text;
        message.className = isError
            ? "message error"
            : "message success";
    }

    async function loadVehicles() {
        results.textContent = "Loading vehicles...";

        try {
            const response = await fetch(`${API_URL}/vehicles`);

            if (!response.ok) {
                throw new Error("Could not load vehicles");
            }

            const vehicles = await response.json();

            results.replaceChildren();

            if (vehicles.length === 0) {
                results.textContent = "No vehicles saved yet.";
                return;
            }

            vehicles.forEach(vehicle => {
                const card = document.createElement("div");
                card.className = "vehicle";

                const details = document.createElement("p");
                details.textContent =
                    `Vehicle ID: ${vehicle.vehicle_id} | ` +
                    `Status: ${vehicle.status} | ` +
                    `Latitude: ${vehicle.latitude} | ` +
                    `Longitude: ${vehicle.longitude}`;

                card.appendChild(details);
                results.appendChild(card);
            });

        } catch (error) {
            results.textContent =
                "Unable to load vehicles. Check that the backend is running.";
            console.error(error);
        }
    }

    form.addEventListener("submit", async function(event) {
        event.preventDefault();

        const vehicle = {
            vehicle_id: document.getElementById("vehicleId").value.trim(),
            status: document.getElementById("status").value,
            latitude: Number(document.getElementById("latitude").value),
            longitude: Number(document.getElementById("longitude").value)
        };

        if (!vehicle.vehicle_id) {
            showMessage("Please enter a vehicle ID.", true);
            return;
        }

        if (
            !Number.isFinite(vehicle.latitude) ||
            vehicle.latitude < -90 ||
            vehicle.latitude > 90 ||
            !Number.isFinite(vehicle.longitude) ||
            vehicle.longitude < -180 ||
            vehicle.longitude > 180
        ) {
            showMessage("Please enter valid latitude and longitude.", true);
            return;
        }

        const submitButton = form.querySelector(
            'button[type="submit"]'
        );

        submitButton.disabled = true;
        showMessage("Saving vehicle...");

        try {
            const response = await fetch(`${API_URL}/vehicles`, {
                method: "POST",
                headers: {
                    "Content-Type": "application/json"
                },
                body: JSON.stringify(vehicle)
            });

            const data = await response.json();

            if (!response.ok) {
                throw new Error(
                    data.detail || "Failed to save vehicle"
                );
            }

            showMessage("Vehicle added successfully!");
            form.reset();

            await loadVehicles();

        } catch (error) {
            showMessage("Error: " + error.message, true);
            console.error(error);
        } finally {
            submitButton.disabled = false;
        }
    });

    document.getElementById("refreshButton")
        .addEventListener("click", loadVehicles);

    loadVehicles();
</script>

</body>
</html>






