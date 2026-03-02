import { useState, useEffect, useRef } from "react";

const TypewriterText = ({ texts, speed = 80 }) => {
  const [displayed, setDisplayed] = useState("");
  const [textIndex, setTextIndex] = useState(0);
  const [charIndex, setCharIndex] = useState(0);
  const [deleting, setDeleting] = useState(false);

  useEffect(() => {
    const current = texts[textIndex];
    const timeout = setTimeout(() => {
      if (!deleting) {
        if (charIndex < current.length) {
          setDisplayed(current.slice(0, charIndex + 1));
          setCharIndex(c => c + 1);
        } else {
          setTimeout(() => setDeleting(true), 1500);
        }
      } else {
        if (charIndex > 0) {
          setDisplayed(current.slice(0, charIndex - 1));
          setCharIndex(c => c - 1);
        } else {
          setDeleting(false);
          setTextIndex(i => (i + 1) % texts.length);
        }
      }
    }, deleting ? 40 : speed);
    return () => clearTimeout(timeout);
  }, [charIndex, deleting, textIndex, texts, speed]);

  return (
    <span className="text-emerald-400 font-mono">
      {displayed}<span className="animate-pulse">|</span>
    </span>
  );
};

const AnimatedBar = ({ label, value, color, delay }) => {
  const [width, setWidth] = useState(0);
  useEffect(() => {
    const t = setTimeout(() => setWidth(value), 300 + delay);
    return () => clearTimeout(t);
  }, [value, delay]);

  return (
    <div className="mb-3">
      <div className="flex justify-between text-xs mb-1">
        <span className="text-gray-300 font-mono">{label}</span>
        <span style={{ color }} className="font-mono font-bold">{value}%</span>
      </div>
      <div className="h-2 bg-gray-800 rounded-full overflow-hidden">
        <div
          className="h-full rounded-full transition-all duration-1000 ease-out"
          style={{ width: `${width}%`, background: `linear-gradient(90deg, ${color}88, ${color})` }}
        />
      </div>
    </div>
  );
};

const FloatingParticle = ({ style }) => (
  <div
    className="absolute rounded-full opacity-20 animate-pulse"
    style={style}
  />
);

const ProjectCard = ({ title, desc, stack, link, color, delay }) => {
  const [visible, setVisible] = useState(false);
  useEffect(() => {
    const t = setTimeout(() => setVisible(true), delay);
    return () => clearTimeout(t);
  }, [delay]);

  return (
    <div
      className="border rounded-xl p-4 transition-all duration-700 cursor-pointer group hover:scale-105"
      style={{
        borderColor: `${color}44`,
        background: `linear-gradient(135deg, #0d1117, ${color}11)`,
        opacity: visible ? 1 : 0,
        transform: visible ? "translateY(0)" : "translateY(20px)",
        transition: `all 0.6s ease ${delay}ms`,
      }}
    >
      <div className="flex items-center gap-2 mb-2">
        <div className="w-3 h-3 rounded-full" style={{ backgroundColor: color }} />
        <span className="text-white font-bold text-sm font-mono">{title}</span>
      </div>
      <p className="text-gray-400 text-xs mb-3 leading-relaxed">{desc}</p>
      <div className="flex flex-wrap gap-1 mb-3">
        {stack.map(s => (
          <span key={s} className="text-xs px-2 py-0.5 rounded-full font-mono"
            style={{ background: `${color}22`, color, border: `1px solid ${color}44` }}>
            {s}
          </span>
        ))}
      </div>
      <a href={link} target="_blank" rel="noreferrer"
        className="text-xs font-mono flex items-center gap-1 group-hover:gap-2 transition-all"
        style={{ color }}>
        🔗 View Live →
      </a>
    </div>
  );
};

const StatCard = ({ icon, value, label, color, delay }) => {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const t = setTimeout(() => {
      const num = parseInt(value);
      if (!isNaN(num)) {
        let start = 0;
        const step = Math.ceil(num / 30);
        const interval = setInterval(() => {
          start += step;
          if (start >= num) { setCount(num); clearInterval(interval); }
          else setCount(start);
        }, 40);
        return () => clearInterval(interval);
      }
    }, delay);
    return () => clearTimeout(t);
  }, [value, delay]);

  return (
    <div className="rounded-xl p-4 text-center border transition-all hover:scale-105"
      style={{ background: `${color}11`, borderColor: `${color}33` }}>
      <div className="text-2xl mb-1">{icon}</div>
      <div className="text-2xl font-bold font-mono" style={{ color }}>
        {typeof value === "number" || !isNaN(parseInt(value)) ? count : value}+
      </div>
      <div className="text-gray-400 text-xs mt-1">{label}</div>
    </div>
  );
};

export default function GitHubProfile() {
  const [loaded, setLoaded] = useState(false);
  const particles = Array.from({ length: 12 }, (_, i) => ({
    width: `${Math.random() * 6 + 2}px`,
    height: `${Math.random() * 6 + 2}px`,
    top: `${Math.random() * 100}%`,
    left: `${Math.random() * 100}%`,
    background: ["#00ff88", "#0ea5e9", "#a855f7", "#f59e0b"][i % 4],
    animationDelay: `${Math.random() * 3}s`,
    animationDuration: `${2 + Math.random() * 3}s`,
  }));

  useEffect(() => {
    setTimeout(() => setLoaded(true), 100);
  }, []);

  const skills = [
    { label: "Next.js / React", value: 88, color: "#00ff88" },
    { label: "TypeScript", value: 72, color: "#0ea5e9" },
    { label: "Tailwind CSS", value: 90, color: "#38bdf8" },
    { label: "PostgreSQL / MongoDB", value: 65, color: "#a855f7" },
    { label: "Node.js / NestJS", value: 45, color: "#f59e0b" },
  ];

  const projects = [
    {
      title: "WorkPoint",
      desc: "Coworking booking platform · Top 5 @ Riwi Academy · Mercado Pago integration",
      stack: ["React", "Tailwind", "Mercado Pago", "Axios"],
      link: "https://proyecto-integrador-eight-pi.vercel.app/Home",
      color: "#00ff88",
    },
    {
      title: "Tienda Paisa",
      desc: "Self-managed e-commerce for local businesses · WhatsApp checkout · QR Marketing",
      stack: ["Next.js", "PostgreSQL", "Prisma", "Tailwind"],
      link: "https://tienda-paisa.vercel.app/",
      color: "#0ea5e9",
    },
    {
      title: "El Paisita",
      desc: "Interior design landing page · Mobile-first · WhatsApp lead conversion",
      stack: ["Next.js", "Tailwind", "Lucide", "Vercel"],
      link: "https://el-paisita.vercel.app/",
      color: "#a855f7",
    },
  ];

  return (
    <div className="min-h-screen font-mono text-white overflow-hidden"
      style={{ background: "#0d1117" }}>

      {/* Particles */}
      <div className="fixed inset-0 pointer-events-none overflow-hidden">
        {particles.map((p, i) => <FloatingParticle key={i} style={p} />)}
        {/* Grid overlay */}
        <div className="absolute inset-0 opacity-5"
          style={{
            backgroundImage: "linear-gradient(#00ff8833 1px, transparent 1px), linear-gradient(90deg, #00ff8833 1px, transparent 1px)",
            backgroundSize: "40px 40px"
          }} />
      </div>

      <div className="relative max-w-3xl mx-auto px-4 py-10"
        style={{ opacity: loaded ? 1 : 0, transition: "opacity 0.5s" }}>

        {/* Header */}
        <div className="text-center mb-10">
          <div className="relative inline-block mb-4">
            <div className="w-24 h-24 rounded-full mx-auto border-2 border-emerald-400 flex items-center justify-center text-4xl"
              style={{ background: "linear-gradient(135deg, #00ff8822, #0ea5e922)" }}>
              👨‍💻
            </div>
            <div className="absolute -bottom-1 -right-1 w-6 h-6 bg-emerald-400 rounded-full flex items-center justify-center text-xs">
              🇨🇴
            </div>
          </div>

          <h1 className="text-3xl font-bold mb-1"
            style={{ background: "linear-gradient(90deg, #00ff88, #0ea5e9)", WebkitBackgroundClip: "text", WebkitTextFillColor: "transparent" }}>
            David Agudelo Ocampo
          </h1>

          <div className="text-gray-400 text-sm mb-3">
            <TypewriterText texts={[
              "Frontend Developer",
              "Next.js & React Specialist",
              "Building real apps, not just tutorials",
              "Fullstack in Progress 🚀",
            ]} />
          </div>

          <div className="flex justify-center gap-3 text-xs text-gray-500">
            <span>📍 Medellín, Colombia</span>
            <span>🎓 Ing. Software @ TdeA</span>
            <span>🏆 Top 5 @ Riwi</span>
          </div>
        </div>

        {/* Stats */}
        <div className="grid grid-cols-3 gap-3 mb-8">
          <StatCard icon="🚀" value={3} label="Live Projects" color="#00ff88" delay={200} />
          <StatCard icon="⚡" value={1} label="Year Building" color="#0ea5e9" delay={350} />
          <StatCard icon="🏆" value="Top 5" label="Riwi Academy" color="#a855f7" delay={500} />
        </div>

        {/* Skills */}
        <div className="rounded-2xl border border-gray-800 p-5 mb-6"
          style={{ background: "linear-gradient(135deg, #0d1117, #161b22)" }}>
          <h2 className="text-emerald-400 text-sm font-bold mb-4 flex items-center gap-2">
            <span>▸</span> skill_levels.json
          </h2>
          {skills.map((s, i) => (
            <AnimatedBar key={s.label} {...s} delay={i * 150} />
          ))}
        </div>

        {/* Projects */}
        <div className="mb-6">
          <h2 className="text-emerald-400 text-sm font-bold mb-4 flex items-center gap-2">
            <span>▸</span> featured_projects/
          </h2>
          <div className="grid gap-3">
            {projects.map((p, i) => (
              <ProjectCard key={p.title} {...p} delay={200 + i * 150} />
            ))}
          </div>
        </div>

        {/* Tech Stack */}
        <div className="rounded-2xl border border-gray-800 p-5 mb-6"
          style={{ background: "linear-gradient(135deg, #0d1117, #161b22)" }}>
          <h2 className="text-emerald-400 text-sm font-bold mb-4 flex items-center gap-2">
            <span>▸</span> tech_stack.json
          </h2>
          <div className="flex flex-wrap gap-2">
            {["Next.js", "React", "TypeScript", "Tailwind CSS", "PostgreSQL", "MongoDB", "Prisma", "Node.js", "NestJS", "Vercel", "Git", "Postman"].map((tech, i) => (
              <span key={tech} className="px-3 py-1 rounded-full text-xs border transition-all hover:scale-110 cursor-default"
                style={{
                  borderColor: ["#00ff88", "#0ea5e9", "#a855f7", "#f59e0b"][i % 4] + "66",
                  color: ["#00ff88", "#0ea5e9", "#a855f7", "#f59e0b"][i % 4],
                  background: ["#00ff88", "#0ea5e9", "#a855f7", "#f59e0b"][i % 4] + "11",
                }}>
                {tech}
              </span>
            ))}
          </div>
        </div>

        {/* Contact */}
        <div className="rounded-2xl border border-emerald-400/30 p-5 text-center"
          style={{ background: "linear-gradient(135deg, #00ff8808, #0ea5e908)" }}>
          <p className="text-gray-400 text-xs mb-3">
            💬 Open to freelance projects & junior positions
          </p>
          <div className="flex justify-center gap-4">
            <a href="mailto:agudeloocampodavid@gmail.com"
              className="text-xs px-4 py-2 rounded-full border border-emerald-400/50 text-emerald-400 hover:bg-emerald-400/10 transition-all">
              📧 Gmail
            </a>
            <a href="https://www.linkedin.com/in/david-agudelo-ocampo-40b505348/" target="_blank" rel="noreferrer"
              className="text-xs px-4 py-2 rounded-full border border-sky-400/50 text-sky-400 hover:bg-sky-400/10 transition-all">
              💼 LinkedIn
            </a>
          </div>
        </div>

        <p className="text-center text-gray-700 text-xs mt-6">
          ⚡ Preview de GitHub Profile README · David Agudelo Ocampo
        </p>
      </div>
    </div>
  );
}
