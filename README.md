# Repocodeimport React, { useState, useEffect } from "react";
import { BrowserRouter as Router, Routes, Route, Link } from "react-router-dom";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import { useSpring, animated } from "react-spring";
import { TextField } from "@mui/material";
import { Editor } from "@tinymce/tinymce-react";

const Counter = () => {
  const [count, setCount] = useState(0);
  const background = useSpring({
    backgroundColor: `rgba(0, 150, 255, ${Math.min(count / 10, 1)})`,
  });

  return (
    <animated.div style={{ ...background, padding: "20px" }}>
      <h2>Counter: {count}</h2>
      <Button onClick={() => setCount(count + 1)}>Increment</Button>
      <Button onClick={() => setCount(count - 1)}>Decrement</Button>
      <Button onClick={() => setCount(0)}>Reset</Button>
    </animated.div>
  );
};

const UserForm = () => {
  const [userData, setUserData] = useState({ name: "", address: "", email: "", phone: "" });
  const [userID, setUserID] = useState(null);
  const [hasUnsavedChanges, setHasUnsavedChanges] = useState(false);

  useEffect(() => {
    setUserID(crypto.randomUUID());
  }, []);

  useEffect(() => {
    const handleBeforeUnload = (e) => {
      if (hasUnsavedChanges) {
        e.preventDefault();
        e.returnValue = "You have unsaved changes!";
      }
    };
    window.addEventListener("beforeunload", handleBeforeUnload);
    return () => window.removeEventListener("beforeunload", handleBeforeUnload);
  }, [hasUnsavedChanges]);

  return (
    <form>
      <TextField label="Name" onChange={(e) => { setUserData({ ...userData, name: e.target.value }); setHasUnsavedChanges(true); }} />
      <TextField label="Address" onChange={(e) => { setUserData({ ...userData, address: e.target.value }); setHasUnsavedChanges(true); }} />
      <TextField label="Email" onChange={(e) => { setUserData({ ...userData, email: e.target.value }); setHasUnsavedChanges(true); }} />
      <TextField label="Phone" onChange={(e) => { setUserData({ ...userData, phone: e.target.value }); setHasUnsavedChanges(true); }} />
      <Button onClick={() => { localStorage.setItem("userData", JSON.stringify(userData)); setHasUnsavedChanges(false); }}>Save</Button>
    </form>
  );
};

const RichTextEditor = () => {
  const [content, setContent] = useState("");
  return (
    <Editor
      apiKey="your-tinymce-api-key"
      init={{
        menubar: false,
        plugins: "lists link image table code help wordcount",
        toolbar: "undo redo | bold italic underline | alignleft aligncenter alignright alignjustify | bullist numlist outdent indent",
      }}
      onEditorChange={(newValue) => setContent(newValue)}
    />
  );
};

const App = () => {
  return (
    <Router>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/counter">Counter</Link>
        <Link to="/form">User Form</Link>
        <Link to="/editor">Editor</Link>
      </nav>
      <Routes>
        <Route path="/counter" element={<Counter />} />
        <Route path="/form" element={<UserForm />} />
        <Route path="/editor" element={<RichTextEditor />} />
      </Routes>
    </Router>
  );
};

export default App;
