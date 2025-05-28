# Celebel-project-
import { useState } from "react";
import { BrowserRouter as Router, Route, Routes, useNavigate, useLocation } from "react-router-dom";

const countries = {
  India: ["Mumbai", "Delhi", "Bangalore"],
  USA: ["New York", "Los Angeles", "Chicago"],
  UK: ["London", "Manchester", "Liverpool"]
};

const initialFormData = {
  firstName: "",
  lastName: "",
  username: "",
  email: "",
  password: "",
  showPassword: false,
  phoneCode: "",
  phoneNumber: "",
  country: "",
  city: "",
  pan: "",
  aadhar: ""
};

function Form() {
  const [formData, setFormData] = useState(initialFormData);
  const [errors, setErrors] = useState({});
  const navigate = useNavigate();

  const handleChange = ({ target: { name, value } }) => {
    setFormData(prev => ({ ...prev, [name]: value }));
    setErrors(prev => ({ ...prev, [name]: "" }));
  };

  const validate = () => {
    const newErrors = Object.entries(formData).reduce((acc, [key, value]) => {
      if (key !== "showPassword" && !value) acc[key] = "This field is required";
      return acc;
    }, {});
    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };

  const handleSubmit = e => {
    e.preventDefault();
    if (validate()) navigate("/details", { state: formData });
  };

  const isFormValid = Object.entries(formData).every(([key, val]) => key === "showPassword" || val);

  return (
    <form onSubmit={handleSubmit} className="space-y-4 p-4 max-w-lg mx-auto">
      <h2 className="text-2xl font-bold">Registration Form</h2>
      {[
        ["First Name", "firstName"],
        ["Last Name", "lastName"],
        ["Username", "username"],
        ["Email", "email", "email"],
        ["PAN No.", "pan"],
        ["Aadhar No.", "aadhar"]
      ].map(([label, name, type = "text"]) => (
        <div key={name}>
          <label className="block font-semibold">{label}</label>
          <input
            type={type}
            name={name}
            value={formData[name]}
            onChange={handleChange}
            className="border w-full p-2 rounded"
          />
          {errors[name] && <p className="text-red-500 text-sm">{errors[name]}</p>}
        </div>
      ))}

      <div>
        <label className="block font-semibold">Password</label>
        <input
          type={formData.showPassword ? "text" : "password"}
          name="password"
          value={formData.password}
          onChange={handleChange}
          className="border w-full p-2 rounded"
        />
        <label className="inline-flex items-center mt-1">
          <input
            type="checkbox"
            onChange={() => setFormData(prev => ({ ...prev, showPassword: !prev.showPassword }))}
          />
          <span className="ml-2">Show Password</span>
        </label>
        {errors.password && <p className="text-red-500 text-sm">{errors.password}</p>}
      </div>

      <div>
        <label className="block font-semibold">Phone Number</label>
        <div className="flex gap-2">
          <input
            name="phoneCode"
            placeholder="Country Code"
            value={formData.phoneCode}
            onChange={handleChange}
            className="border w-1/3 p-2 rounded"
          />
          <input
            name="phoneNumber"
            placeholder="Number"
            value={formData.phoneNumber}
            onChange={handleChange}
            className="border w-2/3 p-2 rounded"
          />
        </div>
        {(errors.phoneCode || errors.phoneNumber) && <p className="text-red-500 text-sm">Phone code and number are required</p>}
      </div>

      <div>
        <label className="block font-semibold">Country</label>
        <select
          name="country"
          value={formData.country}
          onChange={e => {
            handleChange(e);
            setFormData(prev => ({ ...prev, city: "" }));
          }}
          className="border w-full p-2 rounded"
        >
          <option value="">Select Country</option>
          {Object.keys(countries).map(country => (
            <option key={country} value={country}>{country}</option>
          ))}
        </select>
        {errors.country && <p className="text-red-500 text-sm">{errors.country}</p>}
      </div>

      <div>
        <label className="block font-semibold">City</label>
        <select
          name="city"
          value={formData.city}
          onChange={handleChange}
          className="border w-full p-2 rounded"
          disabled={!formData.country}
        >
          <option value="">Select City</option>
          {(countries[formData.country] || []).map(city => (
            <option key={city} value={city}>{city}</option>
          ))}
        </select>
        {errors.city && <p className="text-red-500 text-sm">{errors.city}</p>}
      </div>

      <button
        type="submit"
        disabled={!isFormValid}
        className="bg-blue-500 text-white p-2 rounded disabled:opacity-50"
      >
        Submit
      </button>
    </form>
  );
}

function Details() {
  const { state } = useLocation();
  return (
    <div className="p-4">
      <h2 className="text-xl font-bold">Submitted Details</h2>
      <pre className="bg-gray-100 p-4 rounded mt-2">{JSON.stringify(state, null, 2)}</pre>
    </div>
  );
}

export default function App() {
  return (
    <Router>
      <Routes>
        <Route path="/" element={<Form />} />
        <Route path="/details" element={<Details />} />
      </Routes>
    </Router>
  );
}
