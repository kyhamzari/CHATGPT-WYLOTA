// 📱 THE WAZOBIA YOUTH LEADERS OF TOMORROW ASSOCIATION
// Mobile App Version (React Native + Expo)

import React, { useState, useEffect } from "react";
import {
  View,
  Text,
  TextInput,
  TouchableOpacity,
  FlatList,
  ScrollView,
  StyleSheet,
  Alert
} from "react-native";

export default function App() {
  const [members, setMembers] = useState([]);
  const [events, setEvents] = useState([
    { id: "1", title: "Leadership Bootcamp", date: "2026-04-12" },
    { id: "2", title: "Community Outreach", date: "2026-05-05" }
  ]);

  const [form, setForm] = useState({ name: "", email: "", interest: "" });
  const [adminLoggedIn, setAdminLoggedIn] = useState(false);
  const [login, setLogin] = useState({ email: "", password: "" });
  const [newEvent, setNewEvent] = useState({ title: "", date: "" });

  const handleRegister = () => {
    if (!form.name || !form.email) {
      Alert.alert("Error", "Name and Email are required");
      return;
    }

    setMembers([...members, { ...form, id: Date.now().toString() }]);
    Alert.alert("Success", "Registration successful. Confirmation email sent.");
    setForm({ name: "", email: "", interest: "" });
  };

  const handleAdminLogin = () => {
    if (login.email === "admin@wazobia.org" && login.password === "admin123") {
      setAdminLoggedIn(true);
    } else {
      Alert.alert("Access Denied", "Invalid admin credentials");
    }
  };

  const handleAddEvent = () => {
    if (!newEvent.title || !newEvent.date) return;

    setEvents([
      ...events,
      { ...newEvent, id: Date.now().toString() }
    ]);
    setNewEvent({ title: "", date: "" });
  };

  return (
    <ScrollView style={styles.container}>
      <Text style={styles.header}>
        THE WAZOBIA YOUTH LEADERS OF TOMORROW ASSOCIATION
      </Text>

      {/* About */}
      <View style={styles.card}>
        <Text style={styles.sectionTitle}>About Us</Text>
        <Text>
          Empowering youth through leadership, innovation, and community development.
        </Text>
      </View>

      {/* Events */}
      <View style={styles.card}>
        <Text style={styles.sectionTitle}>Upcoming Events</Text>
        <FlatList
          data={events}
          keyExtractor={(item) => item.id}
          renderItem={({ item }) => (
            <Text style={styles.listItem}>
              {item.title} - {item.date}
            </Text>
          )}
        />
      </View>

      {/* Registration */}
      <View style={styles.card}>
        <Text style={styles.sectionTitle}>Become a Member</Text>
        <TextInput
          style={styles.input}
          placeholder="Full Name"
          value={form.name}
          onChangeText={(text) => setForm({ ...form, name: text })}
        />
        <TextInput
          style={styles.input}
          placeholder="Email"
          value={form.email}
          onChangeText={(text) => setForm({ ...form, email: text })}
        />
        <TextInput
          style={styles.input}
          placeholder="Interest"
          value={form.interest}
          onChangeText={(text) => setForm({ ...form, interest: text })}
        />
        <TouchableOpacity style={styles.button} onPress={handleRegister}>
          <Text style={styles.buttonText}>Register</Text>
        </TouchableOpacity>
      </View>

      {/* Payment */}
      <View style={styles.card}>
        <Text style={styles.sectionTitle}>Membership Dues</Text>
        <TouchableOpacity
          style={styles.payButton}
          onPress={() => Alert.alert("Payment", "Redirect to payment gateway")}
        >
          <Text style={styles.buttonText}>Pay Membership Dues</Text>
        </TouchableOpacity>
      </View>

      {/* Admin Login */}
      {!adminLoggedIn && (
        <View style={styles.card}>
          <Text style={styles.sectionTitle}>Admin Login</Text>
          <TextInput
            style={styles.input}
            placeholder="Admin Email"
            value={login.email}
            onChangeText={(text) => setLogin({ ...login, email: text })}
          />
          <TextInput
            style={styles.input}
            placeholder="Password"
            secureTextEntry
            value={login.password}
            onChangeText={(text) => setLogin({ ...login, password: text })}
          />
          <TouchableOpacity style={styles.button} onPress={handleAdminLogin}>
            <Text style={styles.buttonText}>Login</Text>
          </TouchableOpacity>
        </View>
      )}

      {/* Admin Dashboard */}
      {adminLoggedIn && (
        <View style={styles.card}>
          <Text style={styles.sectionTitle}>Admin Dashboard</Text>
          <Text style={{ fontWeight: "bold" }}>Registered Members:</Text>
          {members.map((m) => (
            <Text key={m.id} style={styles.listItem}>
              {m.name} - {m.email}
            </Text>
          ))}

          <Text style={{ fontWeight: "bold", marginTop: 10 }}>Add Event:</Text>
          <TextInput
            style={styles.input}
            placeholder="Event Title"
            value={newEvent.title}
            onChangeText={(text) => setNewEvent({ ...newEvent, title: text })}
          />
          <TextInput
            style={styles.input}
            placeholder="Event Date"
            value={newEvent.date}
            onChangeText={(text) => setNewEvent({ ...newEvent, date: text })}
          />
          <TouchableOpacity style={styles.button} onPress={handleAddEvent}>
            <Text style={styles.buttonText}>Add Event</Text>
          </TouchableOpacity>
        </View>
      )}

      <Text style={styles.footer}>© 2026 Wazobia Youth Leaders</Text>
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: "#f4f4f4",
    padding: 20
  },
  header: {
    fontSize: 20,
    fontWeight: "bold",
    textAlign: "center",
    marginBottom: 20,
    color: "green"
  },
  card: {
    backgroundColor: "#ffffff",
    padding: 15,
    borderRadius: 10,
    marginBottom: 15
  },
  sectionTitle: {
    fontSize: 18,
    fontWeight: "bold",
    marginBottom: 10
  },
  input: {
    borderWidth: 1,
    borderColor: "#ccc",
    padding: 10,
    marginBottom: 10,
    borderRadius: 5
  },
  button: {
    backgroundColor: "green",
    padding: 12,
    borderRadius: 5,
    alignItems: "center"
  },
  payButton: {
    backgroundColor: "orange",
    padding: 12,
    borderRadius: 5,
    alignItems: "center"
  },
  buttonText: {
    color: "white",
    fontWeight: "bold"
  },
  listItem: {
    marginBottom: 5
  },
  footer: {
    textAlign: "center",
    marginVertical: 20,
    color: "gray"
  }
});
