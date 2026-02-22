import React, { useState, useEffect } from "react";
import { initializeApp } from "firebase/app";
import { getAuth, signInWithEmailAndPassword, signOut, onAuthStateChanged } from "firebase/auth";
import { getFirestore, collection, addDoc, getDocs } from "firebase/firestore";
import { motion } from "framer-motion";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Textarea } from "@/components/ui/textarea";
import { Users, Calendar, Heart, Mail, Image, BookOpen, Shield } from "lucide-react";

// 🔥 Firebase Configuration (Replace with your own Firebase project details)
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_AUTH_DOMAIN",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_STORAGE_BUCKET",
  messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
  appId: "YOUR_APP_ID"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);
const auth = getAuth(app);

export default function WazobiaYouthWebsite() {
  const [admin, setAdmin] = useState(null);
  const [loginForm, setLoginForm] = useState({ email: "", password: "" });

  useEffect(() => {
    const unsubscribe = onAuthStateChanged(auth, (user) => {
      setAdmin(user);
    });
    return () => unsubscribe();
  }, []);

  const handleAdminLogin = async () => {
    try {
      await signInWithEmailAndPassword(auth, loginForm.email, loginForm.password);
      setLoginForm({ email: "", password: "" });
    } catch (error) {
      alert("Invalid admin credentials");
    }
  };

  const handleLogout = async () => {
    await signOut(auth);
  };
  const [members, setMembers] = useState([]);
  const [events, setEvents] = useState([
    { title: "Leadership Bootcamp", date: "2026-04-12" },
    { title: "Community Outreach", date: "2026-05-05" },
  ]);

  const [form, setForm] = useState({ name: "", email: "", interest: "" });
  const [newEvent, setNewEvent] = useState({ title: "", date: "" });

  const handleRegister = async () => {
    if (!form.name || !form.email) return;

    try {
      await addDoc(collection(db, "members"), form);
      
      // 📧 Send Email Receipt (EmailJS)
      if (window.emailjs) {
        window.emailjs.send(
          "YOUR_SERVICE_ID",
          "YOUR_TEMPLATE_ID",
          {
            to_name: form.name,
            to_email: form.email,
            message: "Thank you for registering with THE WAZOBIA YOUTH LEADERS OF TOMORROW ASSOCIATION."
          },
          "YOUR_PUBLIC_KEY"
        );
      }

      setMembers([...members, form]);
      setForm({ name: "", email: "", interest: "" });
    } catch (error) {
      console.error("Error adding member: ", error);
    }
  };

  const handleAddEvent = async () => {
    if (!newEvent.title || !newEvent.date) return;

    try {
      await addDoc(collection(db, "events"), newEvent);
      setEvents([...events, newEvent]);
      setNewEvent({ title: "", date: "" });
    } catch (error) {
      console.error("Error adding event: ", error);
    }
  };

  return (
    <div className="min-h-screen bg-white text-gray-800 scroll-smooth">
      {/* Navigation */}
      <nav className="fixed w-full bg-green-700 text-white shadow-lg z-50">
        <div className="max-w-7xl mx-auto flex justify-between items-center p-4">
          <h1 className="font-bold text-lg">THE WAZOBIA YOUTH LEADERS OF TOMORROW</h1>
          <div className="space-x-4 hidden md:block">
            <a href="#about">About</a>
            <a href="#programs">Programs</a>
            <a href="#events">Events</a>
            <a href="#gallery">Gallery</a>
            <a href="#blog">Blog</a>
            <a href="#contact">Contact</a>
          </div>
        </div>
      </nav>

      <div className="pt-20">
        {/* Hero */}
        <section className="bg-gradient-to-r from-green-600 to-yellow-500 text-white py-24 px-6 text-center">
          <motion.h1
            initial={{ opacity: 0, y: -20 }}
            animate={{ opacity: 1, y: 0 }}
            transition={{ duration: 0.6 }}
            className="text-4xl md:text-6xl font-bold mb-6"
          >
            Empowering Youth. Building Tomorrow.
          </motion.h1>
          <p className="text-lg max-w-2xl mx-auto">
            A united movement shaping future leaders through service, innovation, and unity.
          </p>
        </section>

        {/* About */}
        <section id="about" className="py-16 px-6 max-w-6xl mx-auto grid md:grid-cols-2 gap-10">
          <div>
            <h2 className="text-3xl font-bold mb-4">About Us</h2>
            <p>
              The Wazobia Youth Leaders of Tomorrow is dedicated to nurturing responsible, visionary, and impactful young leaders across communities.
            </p>
          </div>
          <div className="grid grid-cols-2 gap-4">
            <Card className="rounded-2xl shadow-lg">
              <CardContent className="p-6 text-center">
                <Users className="mx-auto mb-2" />
                <h3>Growing Membership</h3>
              </CardContent>
            </Card>
            <Card className="rounded-2xl shadow-lg">
              <CardContent className="p-6 text-center">
                <Heart className="mx-auto mb-2" />
                <h3>Community Impact</h3>
              </CardContent>
            </Card>
          </div>
        </section>

        {/* Programs */}
        <section id="programs" className="bg-gray-100 py-16 px-6">
          <div className="text-center mb-10">
            <h2 className="text-3xl font-bold">Our Programs</h2>
          </div>
          <div className="max-w-6xl mx-auto grid md:grid-cols-3 gap-8">
            {["Leadership Training", "Entrepreneurship", "Community Development"].map((program, index) => (
              <Card key={index} className="rounded-2xl shadow-lg">
                <CardContent className="p-6">
                  <h3 className="text-xl font-semibold mb-2">{program}</h3>
                  <p>Practical initiatives that build skills and character.</p>
                </CardContent>
              </Card>
            ))}
          </div>
        </section>

        {/* Events */}
        <section id="events" className="py-16 px-6 max-w-6xl mx-auto">
          <h2 className="text-3xl font-bold text-center mb-8">Upcoming Events</h2>
          <div className="grid md:grid-cols-2 gap-6">
            {events.map((event, i) => (
              <Card key={i} className="rounded-2xl shadow-lg">
                <CardContent className="p-6">
                  <Calendar className="mb-2" />
                  <h3 className="font-semibold">{event.title}</h3>
                  <p>{event.date}</p>
                </CardContent>
              </Card>
            ))}
          </div>
        </section>

        {/* Gallery */}
        <section id="gallery" className="bg-gray-100 py-16 px-6 text-center">
          <h2 className="text-3xl font-bold mb-8">Photo Gallery</h2>
          <div className="grid md:grid-cols-3 gap-4 max-w-6xl mx-auto">
            {[1, 2, 3].map((img) => (
              <div key={img} className="bg-gray-300 h-48 rounded-2xl flex items-center justify-center">
                <Image />
              </div>
            ))}
          </div>
        </section>

        {/* Blog */}
        <section id="blog" className="py-16 px-6 max-w-6xl mx-auto">
          <h2 className="text-3xl font-bold text-center mb-8">Latest News</h2>
          <Card className="rounded-2xl shadow-lg">
            <CardContent className="p-6">
              <BookOpen className="mb-2" />
              <h3 className="font-semibold text-xl">Youth Leadership Summit Success</h3>
              <p>Our recent summit empowered over 200 young leaders with valuable skills.</p>
            </CardContent>
          </Card>
        </section>

        {/* Registration */}
        <section className="bg-green-50 py-16 px-6 text-center">
          <h2 className="text-3xl font-bold mb-6">Become a Member</h2>
          <div className="max-w-xl mx-auto space-y-4">
            <Input
              placeholder="Full Name"
              value={form.name}
              onChange={(e) => setForm({ ...form, name: e.target.value })}
            />
            <Input
              placeholder="Email"
              value={form.email}
              onChange={(e) => setForm({ ...form, email: e.target.value })}
            />
            <Textarea
              placeholder="Your Interests"
              value={form.interest}
              onChange={(e) => setForm({ ...form, interest: e.target.value })}
            />
            <Button onClick={handleRegister}>Register</Button>
          </div>
        </section>

        {/* Admin Authentication */}
        <section className="py-16 px-6 max-w-4xl mx-auto text-center">
          <h2 className="text-3xl font-bold mb-6">Admin Access</h2>
          {!admin ? (
            <div className="space-y-4 max-w-md mx-auto">
              <Input
                placeholder="Admin Email"
                value={loginForm.email}
                onChange={(e) => setLoginForm({ ...loginForm, email: e.target.value })}
              />
              <Input
                type="password"
                placeholder="Password"
                value={loginForm.password}
                onChange={(e) => setLoginForm({ ...loginForm, password: e.target.value })}
              />
              <Button onClick={handleAdminLogin}>Login</Button>
            </div>
          ) : (
            <div>
              <p className="mb-4 text-green-600 font-semibold">Admin Logged In</p>
              <Button onClick={handleLogout}>Logout</Button>
            </div>
          )}
        </section>

        {/* Admin Dashboard (Protected) */}
        {admin && (
        <section className="py-16 px-6 max-w-6xl mx-auto">
          <h2 className="text-3xl font-bold text-center mb-8 flex items-center justify-center gap-2">
            <Shield /> Admin Dashboard
          </h2>
          <div className="grid md:grid-cols-2 gap-8">
            <Card className="rounded-2xl shadow-lg">
              <CardContent className="p-6">
                <h3 className="font-semibold mb-4">Registered Members</h3>
                {members.length === 0 ? (
                  <p>No members yet.</p>
                ) : (
                  members.map((m, i) => (
                    <p key={i}>{m.name} - {m.email}</p>
                  ))
                )}
              </CardContent>
            </Card>

            <Card className="rounded-2xl shadow-lg">
              <CardContent className="p-6 space-y-3">
                <h3 className="font-semibold">Add New Event</h3>
                <Input
                  placeholder="Event Title"
                  value={newEvent.title}
                  onChange={(e) => setNewEvent({ ...newEvent, title: e.target.value })}
                />
                <Input
                  type="date"
                  value={newEvent.date}
                  onChange={(e) => setNewEvent({ ...newEvent, date: e.target.value })}
                />
                <Button onClick={handleAddEvent}>Add Event</Button>
              </CardContent>
            </Card>
          </div>
        </section>
        )}

        {/* Membership Payment */}
        <section className="bg-yellow-50 py-16 px-6 text-center">
          <h2 className="text-3xl font-bold mb-6">Membership Dues Payment</h2>
          <p className="mb-6">Support the association by paying your annual dues securely.</p>
          <Button
            onClick={() => window.location.href = "https://buy.stripe.com/test_payment_link"}
            className="bg-green-600 text-white"
          >
            Pay Membership Dues
          </Button>
        </section>

        {/* Contact */}
        <section id="contact" className="bg-green-700 text-white py-16 px-6 text-center">
          <h2 className="text-3xl font-bold mb-6">Contact Us</h2>
          <p>Email: info@wazobiayouth.org</p>
          <p>Phone: +234 000 000 0000</p>
        </section>

        {/* Footer */}
        <footer className="bg-gray-900 text-white py-6 text-center">
          <p>© {new Date().getFullYear()} THE WAZOBIA YOUTH LEADERS OF TOMORROW. All rights reserved.</p>
        </footer>
      </div>
    </div>
  );
}
