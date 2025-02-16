# Energy-map.
Energy-map.
import React, { useState, useEffect } from "react";
import { MapContainer, TileLayer, Marker, Popup } from "react-leaflet";
import "leaflet/dist/leaflet.css";
import axios from "axios";

const EnergyMap = () => {
  const [location, setLocation] = useState(null);
  const [energyPoints, setEnergyPoints] = useState([]);

  useEffect(() => {
    navigator.geolocation.getCurrentPosition(
      (position) => {
        setLocation({
          lat: position.coords.latitude,
          lng: position.coords.longitude,
        });
      },
      (error) => {
        console.error("Error obteniendo la ubicación: ", error);
      }
    );
  }, []);

  useEffect(() => {
    if (location) {
      axios
        .get(`https://api-energy-data.com/get?lat=${location.lat}&lng=${location.lng}`)
        .then((response) => {
          setEnergyPoints(response.data);
        })
        .catch((error) => {
          console.error("Error obteniendo los datos: ", error);
        });
    }
  }, [location]);

  return (
    <div style={{ height: "100vh", width: "100%" }}>
      {location ? (
        <MapContainer center={[location.lat, location.lng]} zoom={12} style={{ height: "100%", width: "100%" }}>
          <TileLayer
            url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
            attribution='&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a>'
          />
          <Marker position={[location.lat, location.lng]}>
            <Popup>Tu ubicación actual</Popup>
          </Marker>
          {energyPoints.map((point, index) => (
            <Marker key={index} position={[point.lat, point.lng]}>
              <Popup>{point.name}</Popup>
            </Marker>
          ))}
        </MapContainer>
      ) : (
        <p>Cargando ubicación...</p>
      )}
    </div>
  );
};

export default EnergyMap;
