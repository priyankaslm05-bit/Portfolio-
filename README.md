# Portfolio-
import { useState, useEffect } from "react";

type Theme = "dark" | "light";

export const useTheme = () => {
  const [theme, setTheme] = useState<Theme>(() => {
    // Check localStorage first
    const savedTheme = localStorage.getItem("portfolio-theme") as Theme;
    if (savedTheme) return savedTheme;
    
    // Default to dark theme (matches our design)
    return "dark";
  });

  useEffect(() => {
    const root = document.documentElement;
    
    if (theme === "light") {
      root.classList.add("light");
      root.classList.remove("dark");
    } else {
      root.classList.add("dark");
      root.classList.remove("light");
    }
    
    // Save to localStorage
    localStorage.setItem("portfolio-theme", theme);
  }, [theme]);

  const toggleTheme = () => {
    setTheme(prev => prev === "dark" ? "light" : "dark");
  };

  return { theme, setTheme, toggleTheme };
};

import { useState } from "react";
import { Mail, MapPin, Send, Github, Linkedin, Twitter, CheckCircle } from "lucide-react";
import { z } from "zod";

// Validation schema
const contactSchema = z.object({
  name: z
    .string()
    .trim()
    .min(1, "Name is required")
    .max(100, "Name must be less than 100 characters"),
  email: z
    .string()
    .trim()
    .min(1, "Email is required")
    .email("Please enter a valid email address")
    .max(255, "Email must be less than 255 characters"),
  message: z
    .string()
    .trim()
    .min(1, "Message is required")
    .min(10, "Message must be at least 10 characters")
    .max(1000, "Message must be less than 1000 characters"),
});

type FormErrors = {
  name?: string;
  email?: string;
  message?: string;
};

const ContactSection = () => {
  const [formData, setFormData] = useState({
    name: "",
    email: "",
    message: "",
  });
  const [errors, setErrors] = useState<FormErrors>({});
  const [isSubmitted, setIsSubmitted] = useState(false);
  const [touched, setTouched] = useState<Record<string, boolean>>({});

  const validateField = (name: string, value: string): string | undefined => {
    try {
      const fieldSchema = contactSchema.shape[name as keyof typeof contactSchema.shape];
      fieldSchema.parse(value);
      return undefined;
    } catch (error) {
      if (error instanceof z.ZodError) {
        return error.errors[0]?.message;
      }
      return "Invalid input";
    }
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    
    // Validate all fields
    const result = contactSchema.safeParse(formData);
    
    if (!result.success) {
      const fieldErrors: FormErrors = {};
      result.error.errors.forEach((err) => {
        const field = err.path[0] as keyof FormErrors;
        if (!fieldErrors[field]) {
          fieldErrors[field] = err.message;
        }
      });
      setErrors(fieldErrors);
      setTouched({ name: true, email: true, message: true });
      return;
    }
    
    // Form is valid - show success state
    setIsSubmitted(true);
    setErrors({});
    
    // Reset form after delay
    setTimeout(() => {
      setFormData({ name: "", email: "", message: "" });
      setIsSubmitted(false);
      setTouched({});
    }, 3000);
  };

  const handleChange = (e: React.ChangeEvent<HTMLInputElement | HTMLTextAreaElement>) => {
    const { name, value } = e.target;
    setFormData({ ...formData, [name]: value });
    
    // Clear error on change if field was touched
    if (touched[name]) {
      const error = validateField(name, value);
      setErrors(prev => ({ ...prev, [name]: error }));
    }
  };

  const handleBlur = (e: React.FocusEvent<HTMLInputElement | HTMLTextAreaElement>) => {
    const { name, value } = e.target;
    setTouched(prev => ({ ...prev, [name]: true }));
    
    // Validate on blur
    const error = validateField(name, value);
    setErrors(prev => ({ ...prev, [name]: error }));
  };

  return (
    <section id="contact" className="py-24 md:py-32 bg-secondary/30">
      <div className="container mx-auto px-6">
        <div className="text-center mb-16">
          <p className="text-primary font-medium tracking-widest uppercase text-sm mb-4">
            Get in Touch
          </p>
          <h2 className="section-title">
            Let's<span className="text-gradient"> Connect</span>
          </h2>
        </div>

        <div className="grid lg:grid-cols-2 gap-16 max-w-6xl mx-auto">
          {/* Contact Info */}
          <div>
            <h3 className="text-2xl font-display font-semibold mb-6">
              Have a project in mind?
            </h3>
            <p className="text-muted-foreground text-lg mb-8">
              I'm always open to discussing new projects, creative ideas, or 
              opportunities to be part of your vision.
            </p>

            <div className="space-y-6 mb-8">
              <div className="flex items-center gap-4">
                <div className="p-3 bg-primary/10 rounded-xl">
                  <Mail className="w-5 h-5 text-primary" />
                </div>
                <div>
                  <p className="text-sm text-muted-foreground">Email</p>
                  <p className="font-medium">hello@example.com</p>
                </div>
              </div>
              <div className="flex items-center gap-4">
                <div className="p-3 bg-primary/10 rounded-xl">
                  <MapPin className="w-5 h-5 text-primary" />
                </div>
                <div>
                  <p className="text-sm text-muted-foreground">Location</p>
                  <p className="font-medium">San Francisco, CA</p>
                </div>
              </div>
            </div>

            {/* Social Links */}
            <div className="flex gap-4">
              <a
                href="#"
                className="p-3 bg-card border border-border rounded-xl hover:border-primary hover:text-primary transition-colors"
              >
                <Github className="w-5 h-5" />
              </a>
              <a
                href="#"
                className="p-3 bg-card border border-border rounded-xl hover:border-primary hover:text-primary transition-colors"
              >
                <Linkedin className="w-5 h-5" />
              </a>
              <a
                href="#"
                className="p-3 bg-card border border-border rounded-xl hover:border-primary hover:text-primary transition-colors"
              >
                <Twitter className="w-5 h-5" />
              </a>
            </div>
          </div>

          {/* Contact Form */}
          <form onSubmit={handleSubmit} className="space-y-6">
            {isSubmitted ? (
              <div className="flex flex-col items-center justify-center py-12 text-center animate-fade-up">
                <div className="p-4 bg-primary/10 rounded-full mb-4">
                  <CheckCircle className="w-12 h-12 text-primary" />
                </div>
                <h3 className="text-2xl font-display font-semibold mb-2">Message Sent!</h3>
                <p className="text-muted-foreground">
                  Thank you for reaching out. I'll get back to you soon.
                </p>
              </div>
            ) : (
              <>
                <div>
                  <label htmlFor="name" className="block text-sm font-medium mb-2">
                    Name
                  </label>
                  <input
                    type="text"
                    id="name"
                    name="name"
                    value={formData.name}
                    onChange={handleChange}
                    onBlur={handleBlur}
                    className={`w-full px-4 py-3 bg-card border rounded-xl focus:outline-none transition-colors ${
                      errors.name && touched.name
                        ? "border-destructive focus:border-destructive"
                        : "border-border focus:border-primary"
                    }`}
                    placeholder="Your name"
                  />
                  {errors.name && touched.name && (
                    <p className="mt-2 text-sm text-destructive animate-fade-up">
                      {errors.name}
                    </p>
                  )}
                </div>
                <div>
                  <label htmlFor="email" className="block text-sm font-medium mb-2">
                    Email
                  </label>
                  <input
                    type="email"
                    id="email"
                    name="email"
                    value={formData.email}
                    onChange={handleChange}
                    onBlur={handleBlur}
                    className={`w-full px-4 py-3 bg-card border rounded-xl focus:outline-none transition-colors ${
                      errors.email && touched.email
                        ? "border-destructive focus:border-destructive"
                        : "border-border focus:border-primary"
                    }`}
                    placeholder="your@email.com"
                  />
                  {errors.email && touched.email && (
                    <p className="mt-2 text-sm text-destructive animate-fade-up">
                      {errors.email}
                    </p>
                  )}
                </div>
                <div>
                  <label htmlFor="message" className="block text-sm font-medium mb-2">
                    Message
                  </label>
                  <textarea
                    id="message"
                    name="message"
                    value={formData.message}
                    onChange={handleChange}
                    onBlur={handleBlur}
                    rows={5}
                    className={`w-full px-4 py-3 bg-card border rounded-xl focus:outline-none transition-colors resize-none ${
                      errors.message && touched.message
                        ? "border-destructive focus:border-destructive"
                        : "border-border focus:border-primary"
                    }`}
                    placeholder="Tell me about your project..."
                  />
                  {errors.message && touched.message && (
                    <p className="mt-2 text-sm text-destructive animate-fade-up">
                      {errors.message}
                    </p>
                  )}
                </div>
                <button
                  type="submit"
                  className="w-full flex items-center justify-center gap-2 px-8 py-4 bg-primary text-primary-foreground font-medium rounded-xl hover:scale-[1.02] transition-transform duration-300 glow-accent"
                >
                  <Send className="w-5 h-5" />
                  Send Message
                </button>
              </>
            )}
          </form>
        </div>
      </div>
    </section>
  );
};

export default ContactSection;

import { useState, useEffect } from "react";
import { Menu, X, Moon, Sun } from "lucide-react";
import { useTheme } from "@/hooks/useTheme";

const navLinks = [
  { name: "About", href: "#about" },
  { name: "Skills", href: "#skills" },
  { name: "Projects", href: "#projects" },
  { name: "Contact", href: "#contact" },
];

const Navigation = () => {
  const [isScrolled, setIsScrolled] = useState(false);
  const [isMobileMenuOpen, setIsMobileMenuOpen] = useState(false);
  const [activeSection, setActiveSection] = useState("");
  const { theme, toggleTheme } = useTheme();

  useEffect(() => {
    const handleScroll = () => {
      setIsScrolled(window.scrollY > 50);
      
      // Determine active section based on scroll position
      const sections = navLinks.map(link => link.href.substring(1));
      const scrollPosition = window.scrollY + 100;
      
      for (let i = sections.length - 1; i >= 0; i--) {
        const section = document.getElementById(sections[i]);
        if (section && section.offsetTop <= scrollPosition) {
          setActiveSection(sections[i]);
          break;
        }
      }
      
      // If at top, no active section
      if (window.scrollY < 100) {
        setActiveSection("");
      }
    };
    
    window.addEventListener("scroll", handleScroll);
    handleScroll(); // Initial check
    return () => window.removeEventListener("scroll", handleScroll);
  }, []);

  return (
    <nav
      className={`fixed top-0 left-0 right-0 z-50 transition-all duration-500 ${
        isScrolled ? "bg-background/80 backdrop-blur-lg border-b border-border" : ""
      }`}
    >
      <div className="container mx-auto px-6 py-4">
        <div className="flex items-center justify-between">
          <a href="#" className="text-2xl font-display font-semibold text-gradient">
            Portfolio
          </a>

          {/* Desktop Navigation */}
          <div className="hidden md:flex items-center gap-8">
            {navLinks.map((link) => (
              <a 
                key={link.name} 
                href={link.href} 
                className={`nav-link text-sm font-medium tracking-wide ${
                  activeSection === link.href.substring(1) 
                    ? "text-primary after:w-full" 
                    : ""
                }`}
              >
                {link.name}
              </a>
            ))}
            
            {/* Dark Mode Toggle */}
            <button
              onClick={toggleTheme}
              className="p-2 rounded-lg bg-secondary hover:bg-secondary/80 transition-colors"
              aria-label="Toggle dark mode"
            >
              {theme === "dark" ? (
                <Sun className="w-5 h-5 text-primary" />
              ) : (
                <Moon className="w-5 h-5 text-foreground" />
              )}
            </button>
          </div>

          {/* Mobile Menu Button */}
          <div className="flex md:hidden items-center gap-2">
            <button
              onClick={toggleTheme}
              className="p-2 rounded-lg bg-secondary hover:bg-secondary/80 transition-colors"
              aria-label="Toggle dark mode"
            >
              {theme === "dark" ? (
                <Sun className="w-5 h-5 text-primary" />
              ) : (
                <Moon className="w-5 h-5 text-foreground" />
              )}
            </button>
            <button
              className="text-foreground"
              onClick={() => setIsMobileMenuOpen(!isMobileMenuOpen)}
            >
              {isMobileMenuOpen ? <X size={24} /> : <Menu size={24} />}
            </button>
          </div>
        </div>

        {/* Mobile Menu */}
        {isMobileMenuOpen && (
          <div className="md:hidden mt-4 pb-4 animate-fade-up">
            <div className="flex flex-col gap-4">
              {navLinks.map((link) => (
                <a
                  key={link.name}
                  href={link.href}
                  className={`nav-link text-lg font-medium ${
                    activeSection === link.href.substring(1) 
                      ? "text-primary" 
                      : ""
                  }`}
                  onClick={() => setIsMobileMenuOpen(false)}
                >
                  {link.name}
                </a>
              ))}
            </div>
          </div>
        )}
      </div>
    </nav>
  );
};

export default Navigation;

@tailwind base;
@tailwind components;
@tailwind utilities;

@import url('https://fonts.googleapis.com/css2?family=Playfair+Display:wght@400;500;600;700&family=Inter:wght@300;400;500;600&display=swap');

@layer base {
  :root {
    --background: 240 10% 4%;
    --foreground: 60 9% 98%;

    --card: 240 10% 6%;
    --card-foreground: 60 9% 98%;

    --popover: 240 10% 6%;
    --popover-foreground: 60 9% 98%;

    --primary: 45 93% 58%;
    --primary-foreground: 240 10% 4%;

    --secondary: 240 6% 12%;
    --secondary-foreground: 60 9% 98%;

    --muted: 240 6% 15%;
    --muted-foreground: 240 5% 65%;

    --accent: 45 93% 58%;
    --accent-foreground: 240 10% 4%;

    --destructive: 0 84.2% 60.2%;
    --destructive-foreground: 210 40% 98%;

    --border: 240 6% 18%;
    --input: 240 6% 18%;
    --ring: 45 93% 58%;

    --radius: 0.5rem;

    /* Custom tokens */
    --gradient-gold: linear-gradient(135deg, hsl(45, 93%, 58%) 0%, hsl(35, 90%, 50%) 100%);
    --gradient-dark: linear-gradient(180deg, hsl(240, 10%, 4%) 0%, hsl(240, 10%, 8%) 100%);
    --glow-gold: 0 0 40px hsl(45, 93%, 58%, 0.3);
    --shadow-elegant: 0 25px 50px -12px hsl(0, 0%, 0%, 0.5);

    --font-display: 'Playfair Display', serif;
    --font-body: 'Inter', sans-serif;

    --sidebar-background: 240 5.9% 10%;
    --sidebar-foreground: 240 4.8% 95.9%;
    --sidebar-primary: 224.3 76.3% 48%;
    --sidebar-primary-foreground: 0 0% 100%;
    --sidebar-accent: 240 3.7% 15.9%;
    --sidebar-accent-foreground: 240 4.8% 95.9%;
    --sidebar-border: 240 3.7% 15.9%;
    --sidebar-ring: 217.2 91.2% 59.8%;
  }

  .dark {
    --background: 240 10% 4%;
    --foreground: 60 9% 98%;
    --card: 240 10% 6%;
    --card-foreground: 60 9% 98%;
    --popover: 240 10% 6%;
    --popover-foreground: 60 9% 98%;
    --primary: 45 93% 58%;
    --primary-foreground: 240 10% 4%;
    --secondary: 240 6% 12%;
    --secondary-foreground: 60 9% 98%;
    --muted: 240 6% 15%;
    --muted-foreground: 240 5% 65%;
    --accent: 45 93% 58%;
    --accent-foreground: 240 10% 4%;
    --border: 240 6% 18%;
    --input: 240 6% 18%;
  }

  .light {
    --background: 60 9% 98%;
    --foreground: 240 10% 4%;
    --card: 0 0% 100%;
    --card-foreground: 240 10% 4%;
    --popover: 0 0% 100%;
    --popover-foreground: 240 10% 4%;
    --primary: 35 90% 45%;
    --primary-foreground: 60 9% 98%;
    --secondary: 240 5% 92%;
    --secondary-foreground: 240 10% 4%;
    --muted: 240 5% 92%;
    --muted-foreground: 240 5% 40%;
    --accent: 35 90% 45%;
    --accent-foreground: 60 9% 98%;
    --border: 240 6% 85%;
    --input: 240 6% 85%;

    --gradient-gold: linear-gradient(135deg, hsl(35, 90%, 45%) 0%, hsl(25, 85%, 40%) 100%);
  }
}

@layer base {
  * {
    @apply border-border;
  }

  html {
    scroll-behavior: smooth;
  }

  body {
    @apply bg-background text-foreground;
    font-family: var(--font-body);
  }

  h1, h2, h3, h4, h5, h6 {
    font-family: var(--font-display);
  }
}

@layer components {
  .text-gradient {
    background: var(--gradient-gold);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
  }

  .glow-accent {
    box-shadow: var(--glow-gold);
  }

  .nav-link {
    @apply relative text-muted-foreground hover:text-foreground transition-colors duration-300;
  }

  .nav-link::after {
    content: '';
    @apply absolute bottom-0 left-0 w-0 h-[1px] bg-primary transition-all duration-300;
  }

  .nav-link:hover::after {
    @apply w-full;
  }

  .section-title {
    @apply text-5xl md:text-6xl font-semibold tracking-tight;
  }

  .card-hover {
    @apply transition-all duration-500 hover:-translate-y-2;
  }

  .card-hover:hover {
    box-shadow: var(--shadow-elegant);
  }
}

@layer utilities {
  .animation-delay-200 {
    animation-delay: 200ms;
  }
  .animation-delay-400 {
    animation-delay: 400ms;
  }
  .animation-delay-600 {
    animation-delay: 600ms;
  }
}
