# VengenceUI Components Source Code

This document contains all the UI components extracted from the VengenceUI open-source repository.

## `src\components\ui\accordion.tsx`

```tsx
'use client'

import * as React from 'react'
import * as AccordionPrimitive from '@radix-ui/react-accordion'
import { ChevronDownIcon } from 'lucide-react'
import { cn } from '@/lib/utils'

function Accordion({ ...props }: React.ComponentProps<typeof AccordionPrimitive.Root>) {
    return (
        <AccordionPrimitive.Root
            data-slot="accordion"
            {...props}
        />
    )
}

function AccordionItem({ className, ...props }: React.ComponentProps<typeof AccordionPrimitive.Item>) {
    return (
        <AccordionPrimitive.Item
            data-slot="accordion-item"
            className={cn('border-b last:border-b-0', className)}
            {...props}
        />
    )
}

function AccordionTrigger({ className, children, ...props }: React.ComponentProps<typeof AccordionPrimitive.Trigger>) {
    return (
        <AccordionPrimitive.Header className="flex">
            <AccordionPrimitive.Trigger
                data-slot="accordion-trigger"
                className={cn('focus-visible:border-ring focus-visible:ring-ring/50 flex flex-1 items-start justify-between gap-4 rounded-md py-4 text-left text-sm font-medium outline-none transition-all hover:underline focus-visible:ring-[3px] disabled:pointer-events-none disabled:opacity-50 [&[data-state=open]>svg]:rotate-180', className)}
                {...props}>
                {children}
                <ChevronDownIcon className="text-muted-foreground pointer-events-none size-4 shrink-0 translate-y-0.5 transition-transform duration-200" />
            </AccordionPrimitive.Trigger>
        </AccordionPrimitive.Header>
    )
}

function AccordionContent({ className, children, ...props }: React.ComponentProps<typeof AccordionPrimitive.Content>) {
    return (
        <AccordionPrimitive.Content
            data-slot="accordion-content"
            className="data-[state=closed]:animate-accordion-up data-[state=open]:animate-accordion-down overflow-hidden text-sm"
            {...props}>
            <div className={cn('pb-4 pt-0', className)}>{children}</div>
        </AccordionPrimitive.Content>
    )
}

export { Accordion, AccordionItem, AccordionTrigger, AccordionContent }

```

---

## `src\components\ui\agent-bento-grid.tsx`

```tsx
"use client";

import { useState, useEffect } from "react";
import { motion } from "framer-motion";
import {
  ChatCircle,
  Brain,
  Database,
  TerminalWindow,
  Code,
  FileText,
  SlackLogo,
  NotionLogo,
  Check,
  CircleNotch,
  Clock,
  Minus,
  Globe,
} from "@phosphor-icons/react";
import { cn } from "@/lib/utils";

/* ──────────────────────────────────────────────────────
   Niche: AI Agent Workspace
   Grid: 3 cards top row · 2 cards bottom row
   Each FeatCard takes: title, description, children (visual)
────────────────────────────────────────────────────── */

interface FeatCardProps {
  title: string;
  description: string;
  children: React.ReactNode;
  /** Optional extra classes for sizing/spanning */
  className?: string;
}

export function FeatCard({ title, description, children, className = "" }: FeatCardProps) {
  return (
    <motion.div
      initial={{ opacity: 0, y: 16 }}
      animate={{ opacity: 1, y: 0 }}
      transition={{ duration: 0.45, ease: [0.22, 1, 0.36, 1] }}
      className={cn(
        "group relative flex flex-col gap-2 overflow-hidden rounded-[20px] p-4",
        "bg-white dark:bg-neutral-900",
        "shadow-[0_0_0_1px_rgba(0,0,0,0.08),0_2px_4px_rgba(0,0,0,0.04)]",
        "dark:shadow-[inset_0_1px_0_0_rgba(255,255,255,0.05),0_0_0_1px_rgba(255,255,255,0.05),0_2px_4px_rgba(0,0,0,0.2)]",
        className
      )}
    >
      <div className="z-10 flex flex-col gap-1.5">
        <h3 className="font-semibold text-foreground text-sm tracking-tight">{title}</h3>
        <p className="text-muted-foreground text-xs leading-relaxed max-w-[90%]">{description}</p>
      </div>
      <div className="relative mt-2 flex-1 w-full rounded-[14px] overflow-hidden border border-border/50 bg-background/50 dark:bg-neutral-950/50">
        {children}
      </div>
    </motion.div>
  );
}

/* ─────────────────────────────────────────────
   Card1 – Agent Pipeline
   Minimalist precise node graph with real-time task flows
   ───────────────────────────────────────────── */

type ActiveStep = 'request' | 'router' | 'agent' | 'memory' | 'tools' | 'response';

const VW = 320;
const VH = 240;

interface NodeConfig {
  id: string;
  x: number;
  y: number;
  icon?: any;
  label?: string;
  type: 'box' | 'circle';
}

const NODES: NodeConfig[] = [
  { id: 'A', x: 50, y: 120, icon: ChatCircle, label: "REQUEST", type: 'box' },
  { id: 'Router', x: 125, y: 120, type: 'circle' },
  { id: 'C', x: 200, y: 120, icon: Brain, label: "AGENT", type: 'box' },
  { id: 'B', x: 280, y: 50, icon: Database, label: "MEMORY", type: 'box' },
  { id: 'D', x: 280, y: 190, icon: TerminalWindow, label: "TOOLS", type: 'box' },
];

interface FlowPath {
  id: string;
  d: string;
  activeSteps: ActiveStep[];
  flowDirection: 'forward' | 'backward' | 'both';
  colorClass: string;
}

const PATHS: FlowPath[] = [
  {
    id: "a-to-router",
    d: "M 78 120 L 113 120",
    activeSteps: ["request"],
    flowDirection: "forward",
    colorClass: "text-cyan-500 dark:text-cyan-400",
  },
  {
    id: "router-to-agent",
    d: "M 137 120 L 172 120",
    activeSteps: ["agent"],
    flowDirection: "forward",
    colorClass: "text-violet-500 dark:text-violet-400",
  },
  {
    id: "agent-to-memory",
    d: "M 200 92 L 200 50 L 252 50",
    activeSteps: ["memory"],
    flowDirection: "both",
    colorClass: "text-fuchsia-500 dark:text-fuchsia-400",
  },
  {
    id: "agent-to-tools",
    d: "M 200 148 L 200 190 L 252 190",
    activeSteps: ["tools"],
    flowDirection: "both",
    colorClass: "text-emerald-500 dark:text-emerald-400",
  },
  {
    id: "response-flow-1",
    d: "M 172 120 L 137 120",
    activeSteps: ["response"],
    flowDirection: "forward",
    colorClass: "text-cyan-500 dark:text-cyan-400",
  },
  {
    id: "response-flow-2",
    d: "M 113 120 L 78 120",
    activeSteps: ["response"],
    flowDirection: "forward",
    colorClass: "text-cyan-500 dark:text-cyan-400",
  },
];

const NODE_COLORS: Record<string, { bg: string; border: string; text: string; buttonBg: string; buttonBorder: string }> = {
  A: {
    bg: "bg-cyan-500/10 dark:bg-cyan-500/5",
    border: "border-cyan-500/60 dark:border-cyan-400/50",
    text: "text-cyan-600 dark:text-cyan-400",
    buttonBg: "bg-cyan-500",
    buttonBorder: "border-cyan-600",
  },
  Router: {
    bg: "bg-amber-500/10 dark:bg-amber-500/5",
    border: "border-amber-500/60 dark:border-amber-400/50",
    text: "text-amber-600 dark:text-amber-400",
    buttonBg: "bg-amber-500",
    buttonBorder: "border-amber-600",
  },
  C: {
    bg: "bg-violet-500/10 dark:bg-violet-500/5",
    border: "border-violet-500/60 dark:border-violet-400/50",
    text: "text-violet-600 dark:text-violet-400",
    buttonBg: "bg-violet-500",
    buttonBorder: "border-violet-600",
  },
  B: {
    bg: "bg-fuchsia-500/10 dark:bg-fuchsia-500/5",
    border: "border-fuchsia-500/60 dark:border-fuchsia-400/50",
    text: "text-fuchsia-600 dark:text-fuchsia-400",
    buttonBg: "bg-fuchsia-500",
    buttonBorder: "border-fuchsia-600",
  },
  D: {
    bg: "bg-emerald-500/10 dark:bg-emerald-500/5",
    border: "border-emerald-500/60 dark:border-emerald-400/50",
    text: "text-emerald-600 dark:text-emerald-400",
    buttonBg: "bg-emerald-500",
    buttonBorder: "border-emerald-600",
  },
};

export function Card1() {
  const [step, setStep] = useState<ActiveStep>("request");

  useEffect(() => {
    const steps: ActiveStep[] = ["request", "router", "agent", "memory", "tools", "response"];
    let idx = 0;
    const interval = setInterval(() => {
      idx = (idx + 1) % steps.length;
      setStep(steps[idx]);
    }, 2000);
    return () => clearInterval(interval);
  }, []);

  const isNodeActive = (nodeId: string) => {
    switch (step) {
      case 'request':
        return nodeId === 'A';
      case 'router':
        return nodeId === 'Router';
      case 'agent':
        return nodeId === 'C';
      case 'memory':
        return nodeId === 'C' || nodeId === 'B';
      case 'tools':
        return nodeId === 'C' || nodeId === 'D';
      case 'response':
        return nodeId === 'C' || nodeId === 'Router' || nodeId === 'A';
      default:
        return false;
    }
  };

  return (
    <div className="w-full h-full relative overflow-hidden select-none bg-neutral-50 dark:bg-neutral-950/80 rounded-xl flex items-center justify-center p-2">
      {/* ── Layer 1: Clean dotted grid ── */}
      <svg className="absolute inset-0 w-full h-full" aria-hidden>
        <defs>
          <pattern id="clean-grid" width="16" height="16" patternUnits="userSpaceOnUse">
            <circle cx="1.5" cy="1.5" r="0.75" fill="currentColor" className="text-zinc-200 dark:text-zinc-800/60" />
          </pattern>
        </defs>
        <rect width="100%" height="100%" fill="url(#clean-grid)" />
      </svg>

      {/* ── Layer 2: Connector SVG & Nodes ── */}
      <svg
        className="absolute inset-0 w-full h-full"
        viewBox={`0 0 ${VW} ${VH}`}
        preserveAspectRatio="xMidYMid meet"
        aria-hidden
      >
        {/* Base Static Connection Paths */}
        <path d="M 78 120 L 113 120" fill="none" stroke="currentColor" className="text-zinc-200 dark:text-zinc-800/80" strokeWidth="1" />
        <path d="M 137 120 L 172 120" fill="none" stroke="currentColor" className="text-zinc-200 dark:text-zinc-800/80" strokeWidth="1" />
        <path d="M 200 92 L 200 50 L 252 50" fill="none" stroke="currentColor" className="text-zinc-200 dark:text-zinc-800/80" strokeWidth="1" />
        <path d="M 200 148 L 200 190 L 252 190" fill="none" stroke="currentColor" className="text-zinc-200 dark:text-zinc-800/80" strokeWidth="1" />

        {/* Animated Flow Overlays */}
        {PATHS.map((p) => {
          const isActive = p.activeSteps.includes(step);
          if (!isActive) return null;

          return (
            <g key={p.id}>
              {/* Outer soft glow stroke - travels once */}
              <motion.path
                d={p.d}
                fill="none"
                stroke="currentColor"
                className={p.colorClass}
                strokeWidth="3.5"
                strokeOpacity="0.2"
                initial={{ pathLength: 0 }}
                animate={{ pathLength: 1 }}
                transition={{ duration: 0.8, ease: "easeInOut" }}
              />
              {/* Sharp solid flowing stroke - travels once */}
              <motion.path
                d={p.d}
                fill="none"
                stroke="currentColor"
                className={p.colorClass}
                strokeWidth="1.5"
                initial={{ pathLength: 0 }}
                animate={{ pathLength: 1 }}
                transition={{ duration: 0.8, ease: "easeInOut" }}
              />
            </g>
          );
        })}

        {/* ForeignObjects for Nodes */}
        {NODES.map((node) => {
          const isBox = node.type === 'box';
          const w = isBox ? 56 : 24;
          const h = isBox ? 56 : 24;
          const isActive = isNodeActive(node.id);
          const colorStyles = NODE_COLORS[node.id];

          return (
            <foreignObject
              key={node.id}
              x={node.x - w / 2}
              y={node.y - h / 2}
              width={w}
              height={h}
              className="overflow-visible"
            >
              <div className="w-full h-full flex items-center justify-center">
                {isBox && node.icon ? (
                  <div
                    className={`w-full h-full rounded-[14px] border flex flex-col items-center justify-center shadow-[inset_0_1px_0_0_rgba(255,255,255,0.4),inset_4px_4px_0_0_rgba(255,255,255,0.06),inset_6px_6px_0_0_rgba(255,255,255,0.04),inset_8px_8px_0_0_rgba(255,255,255,0.02),0_1px_2px_0_rgba(0,0,0,0.08),0_2px_4px_0_rgba(0,0,0,0.06),0_4px_6px_0_rgba(0,0,0,0.04),0_6px_8px_0_rgba(0,0,0,0.02)] text-white ${colorStyles.buttonBg} ${colorStyles.buttonBorder}`}
                  >
                    {/* Centered Static Icon */}
                    <div className="mb-0.5 flex items-center justify-center">
                      <node.icon className="w-5 h-5" weight="fill" />
                    </div>
                    <span className="text-[8.5px] font-mono font-bold tracking-wider select-none">
                      {node.label}
                    </span>
                  </div>
                ) : (
                  /* Central Router Node Upgrade */
                  <div
                    className={`w-5 h-5 rounded-full border-2 flex items-center justify-center shadow-sm transition-all duration-300 ${isActive
                      ? "bg-amber-500/20 border-amber-500/70"
                      : "bg-background/80 border-zinc-300 dark:border-zinc-800"
                      }`}
                  >
                    <motion.div
                      className={`w-2.5 h-2.5 rounded-full border border-dashed ${isActive ? "border-amber-500" : "border-zinc-400 dark:border-zinc-600"
                        }`}
                      animate={{ rotate: 360 }}
                      transition={{ repeat: Infinity, duration: 4, ease: "linear" }}
                    />
                  </div>
                )}
              </div>
            </foreignObject>
          );
        })}
      </svg>
    </div>
  );
}

/* ─────────────────────────────────────────────
   Card2 – Live Token / Cost Monitor
───────────────────────────────────────────── */
export function Card2() {
  const bars = [45, 75, 35, 85, 60, 95, 50];
  const days = ["MON", "TUE", "WED", "THU", "FRI", "SAT", "SUN"];

  const [activeIdx, setActiveIdx] = useState(0);
  const [hoveredIdx, setHoveredIdx] = useState<number | null>(null);

  useEffect(() => {
    const interval = setInterval(() => {
      setActiveIdx((prev) => (prev === 0 ? 1 : 0));
    }, 3000);
    return () => clearInterval(interval);
  }, []);

  return (
    <div className="w-full h-full flex flex-col gap-3.5 justify-between">
      {/* Stats row with a 0.5rem slide offset margin to prevent slide-up overflow clipping */}
      <div className="flex gap-4 pt-[0.625rem] pr-[0.625rem] pb-0.5 pl-0.5">
        {[
          { label: "Tokens/min", value: "12.4k", trend: "+8%" },
          { label: "Cost/run", value: "$0.042", trend: "-3%" },
        ].map((s, i) => {
          const isActive = i === activeIdx || hoveredIdx === i;

          return (
            <div key={i} className="flex-1 h-[76px] relative select-none">
              {/* Background Hatched Scale Card */}
              <div
                className="absolute inset-0 rounded-xl border border-border/40 dark:border-border/20 bg-muted/5 text-border/30 dark:text-border/20"
                style={{
                  backgroundImage: "repeating-linear-gradient(45deg, transparent, transparent 6px, currentColor 6px, currentColor 7px)",
                }}
              />

              {/* Foreground Card sliding up and right on hover or cycle activation (0.5rem offset) */}
              <motion.div
                className="absolute inset-0 w-full h-full rounded-xl bg-muted/20 dark:bg-neutral-950/80 border border-border/50 shadow-[inset_0_0_0_1px_rgba(255,255,255,1)] dark:shadow-[inset_0_0_0_1px_rgba(255,255,255,0.01)] p-3 hover:bg-muted/30 transition-colors duration-300 backdrop-blur-[2px] flex items-center justify-between gap-3 cursor-pointer"
                animate={{
                  x: isActive ? "0.5rem" : "0rem",
                  y: isActive ? "-0.5rem" : "0rem",
                }}
                transition={{ type: "spring", stiffness: 200, damping: 16 }}
                onMouseEnter={() => setHoveredIdx(i)}
                onMouseLeave={() => setHoveredIdx(null)}
              >
                {/* Left Column: Metric Details */}
                <div className="flex flex-col min-w-0">
                  <span className="text-[8px] text-muted-foreground/80 font-mono uppercase tracking-widest leading-none">{s.label}</span>
                  <span className="text-base font-bold font-mono text-foreground leading-none mt-1.5 tracking-tight">{s.value}</span>
                  <div className="flex items-center gap-1.5 mt-2">
                    <span className={`text-[8px] font-mono font-bold ${s.trend.startsWith("+") ? "text-emerald-500" : "text-rose-400"
                      }`}>
                      {s.trend}
                    </span>
                    <span className="text-[8px] text-muted-foreground/50 font-mono">prev</span>
                  </div>
                </div>

                {/* Right Column: High-Precision Sparkline with Micro Vertices */}
                <div className="w-12 h-6 flex items-center justify-center shrink-0">
                  <svg className="w-full h-full overflow-visible" viewBox="0 0 48 24">
                    {/* Connecting Line */}
                    <motion.path
                      d={i === 0
                        ? "M 0 18 L 16 11 L 32 14 L 48 4"
                        : "M 0 4 L 16 12 L 32 8 L 48 18"
                      }
                      fill="none"
                      stroke="currentColor"
                      className="text-muted-foreground/30 dark:text-muted-foreground/20"
                      strokeWidth="1"
                      strokeLinecap="round"
                      strokeLinejoin="round"
                      initial={{ pathLength: 0 }}
                      animate={{ pathLength: 1 }}
                      transition={{ duration: 0.8, delay: 0.2 + i * 0.15, ease: "easeOut" }}
                    />

                    {/* Vertex Dots */}
                    {(i === 0
                      ? [{ x: 0, y: 18 }, { x: 16, y: 11 }, { x: 32, y: 14 }, { x: 48, y: 4 }]
                      : [{ x: 0, y: 4 }, { x: 16, y: 12 }, { x: 32, y: 8 }, { x: 48, y: 18 }]
                    ).map((pt, idx) => (
                      <motion.circle
                        key={idx}
                        cx={pt.x}
                        cy={pt.y}
                        r="1.5"
                        className="fill-background stroke-muted-foreground/40 dark:stroke-muted-foreground/30"
                        strokeWidth="1"
                        initial={{ scale: 0, opacity: 0 }}
                        animate={{ scale: 1, opacity: 1 }}
                        transition={{ delay: 0.5 + idx * 0.08, duration: 0.25 }}
                      />
                    ))}
                  </svg>
                </div>
              </motion.div>
            </div>
          );
        })}
      </div>

      {/* Bar chart */}
      <div className="flex-1 flex items-end gap-2.5 px-0.5 min-h-[90px]">
        {bars.map((h, i) => (
          <div
            key={i}
            className="flex-1 h-full rounded-xl dark:bg-neutral-950/80 border border-border/80 dark:border-border/30 relative overflow-hidden bg-muted/5 text-border/40 dark:text-border/20"
            style={{
              backgroundImage: "repeating-linear-gradient(45deg, transparent, transparent 6px, currentColor 6px, currentColor 7px)",
            }}
          >
            {/* Animated Solid Filled Bar at bottom */}
            <motion.div
              className="absolute bottom-0 left-0 right-0 bg-primary border-t border-x border-primary/80 shadow-[inset_0_0.5px_0_0_rgba(255,255,255,0.6),inset_0_8px_12px_0_rgba(255,255,255,0.03),inset_0.5px_0_0_0_rgba(255,255,255,0.2),inset_0_2px_6px_0_rgba(255,255,255,0.3),inset_0_-0.5px_0_0_rgba(0,0,0,0.2),inset_-0.5px_0_0_0_rgba(0,0,0,0.1),inset_0_-2px_6px_0_rgba(0,0,0,0.1),0_1px_2px_0_rgba(0,0,0,0.08),0_2px_4px_0_rgba(0,0,0,0.06),0_4px_6px_0_rgba(0,0,0,0.04),inset_0_-4px_8px_0_rgba(0,0,0,0.05)] rounded-t-[10px]"
              initial={{ height: "0%" }}
              animate={{
                height: [
                  `${h}%`,
                  `${Math.min(95, h + 15)}%`,
                  `${Math.max(10, h - 20)}%`,
                  `${Math.min(90, h + 8)}%`,
                  `${h}%`
                ],
              }}
              transition={{
                repeat: Infinity,
                duration: 3 + (i % 3) * 0.8,
                ease: "easeInOut",
                delay: i * 0.1,
              }}
            />
          </div>
        ))}
      </div>

      {/* X labels */}
      <div className="flex gap-2.5 px-0.5">
        {days.map((d, i) => (
          <p key={i} className="flex-1 text-center text-[8px] text-muted-foreground font-mono font-medium">{d}</p>
        ))}
      </div>
    </div>
  );
}

/* ─────────────────────────────────────────────
   Card3 – Stacked Infinite-Scroll Activity Feed
   ───────────────────────────────────────────── */

const STATUS_ICONS: Record<string, { icon: any; color: string; bg: string; gradient: string; border: string }> = {
  done: { icon: Check, color: "text-lime-500", bg: "bg-lime-500/15", gradient: "bg-gradient-to-b from-lime-400 to-lime-600", border: "border-lime-600" },
  running: { icon: CircleNotch, color: "text-blue-400", bg: "bg-blue-400/15", gradient: "bg-gradient-to-b from-blue-400 to-blue-600", border: "border-blue-600" },
  waiting: { icon: Clock, color: "text-amber-400", bg: "bg-amber-400/15", gradient: "bg-gradient-to-b from-amber-400 to-amber-600", border: "border-amber-600" },
  idle: { icon: Minus, color: "text-muted-foreground/60", bg: "bg-muted/40", gradient: "bg-gradient-to-b from-zinc-400 to-zinc-600", border: "border-zinc-600" },
};

export function Card3() {
  const logs = [
    { agent: "Planner", action: "Decomposed task into 4 sub-goals", status: "done", t: "0.2s" },
    { agent: "Researcher", action: "Queried web for latest embeddings", status: "done", t: "1.4s" },
    { agent: "Coder", action: "Generating vector DB schema…", status: "running", t: "3.1s" },
    { agent: "Reviewer", action: "Awaiting output from Coder", status: "waiting", t: "—" },
    { agent: "Writer", action: "Idle — queued", status: "idle", t: "—" },
  ];

  const [activeIdx, setActiveIdx] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setActiveIdx((prev) => (prev + 1) % logs.length);
    }, 2400);
    return () => clearInterval(interval);
  }, [logs.length]);

  // Signed slot: 0 = active front, negative = above (upcoming), positive = below (past)
  const getSlot = (i: number) => {
    const N = logs.length;
    let rel = i - activeIdx;
    if (rel > Math.floor(N / 2)) rel -= N;
    if (rel < -Math.floor(N / 2)) rel += N;
    return rel;
  };

  // Fixed y positions: tighter stack, not linear steps
  const Y: Record<string, number> = { "-2": -68, "-1": -38, "0": 0, "1": 38, "2": 68 };

  return (
    <div className="w-full h-full relative flex items-center justify-center overflow-hidden">
      {logs.map((l, i) => {
        const slot = getSlot(i);
        const si = STATUS_ICONS[l.status];
        const abs = Math.abs(slot);
        const isActive = slot === 0;
        const isVisible = abs <= 2;

        const yOffset = Y[String(slot)] ?? (slot < 0 ? -120 : 120);
        const scale = isActive ? 1 : abs === 1 ? 0.93 : 0.87;
        const opacity = isActive ? 1 : abs === 1 ? 0.65 : 0.38;
        const zIndex = isActive ? 30 : abs === 1 ? 20 : 10;

        return (
          <motion.div
            key={l.agent}
            className="absolute left-0 right-0 mx-auto px-1.5"
            style={{ zIndex }}
            animate={{
              y: isVisible ? yOffset : slot < 0 ? -150 : 150,
              scale,
              opacity: isVisible ? opacity : 0,
            }}
            transition={{
              y: { type: "spring", stiffness: 500, damping: 35 },
              scale: { type: "spring", stiffness: 500, damping: 35 },
              opacity: { duration: 0.25, ease: "easeOut" },
            }}
          >
            <div className={`w-full rounded-2xl border flex items-center gap-2.5 ${isActive
              ? "px-3 py-2.5 bg-background border-border"
              : "px-2.5 py-1.5 bg-muted/30 border-border/50"
              }`}>

              {/* Icon badge — full on active, compact on behind */}
              <div className={`shrink-0 rounded-[8px] flex items-center justify-center font-bold text-white transition-all duration-300 ${si.gradient} border ${si.border} shadow-[inset_0_0.5px_0_0_rgba(255,255,255,0.6),inset_0.5px_0_0_0_rgba(255,255,255,0.2),inset_0_2px_6px_0_rgba(255,255,255,0.3),inset_0_-0.5px_0_0_rgba(0,0,0,0.3),inset_-0.5px_0_0_0_rgba(0,0,0,0.1),inset_0_-2px_6px_0_rgba(0,0,0,0.1),0_1px_2px_0_rgba(0,0,0,0.08),0_2px_4px_0_rgba(0,0,0,0.06),0_4px_6px_0_rgba(0,0,0,0.04)] ${isActive ? "w-8 h-8" : "w-5 h-5"
                }`}>
                <si.icon weight="bold" className={`${isActive ? "w-4 h-4" : "w-2.5 h-2.5"} ${l.status === "running" ? "animate-spin" : ""}`} />
              </div>

              {/* Text — full on active, name-only on behind */}
              <div className="flex-1 min-w-0">
                <div className="flex items-center gap-1.5">
                  <span className={`font-mono font-semibold text-foreground leading-none ${isActive ? "text-[10px]" : "text-[9px]"
                    }`}>{l.agent}</span>
                  <span className={`font-mono uppercase tracking-wide rounded px-1 py-0.5 ${si.bg} ${si.color} ${isActive ? "text-[7px]" : "text-[6px]"
                    }`}>{l.status}</span>
                </div>
                {isActive && (
                  <p className="text-[9px] text-muted-foreground truncate mt-0.5 leading-tight">{l.action}</p>
                )}
              </div>

              {isActive && (
                <span className="text-[9px] font-mono text-muted-foreground shrink-0">{l.t}</span>
              )}
            </div>
          </motion.div>
        );
      })}

      {/* Progress dots */}
      <div className="absolute bottom-1 left-0 right-0 flex justify-center gap-1">
        {logs.map((_, i) => (
          <motion.div
            key={i}
            className="rounded-full bg-foreground/25"
            animate={{
              width: i === activeIdx ? 14 : 4,
              opacity: i === activeIdx ? 0.7 : 0.2,
            }}
            style={{ height: 3 }}
            transition={{ duration: 0.4, ease: [0.16, 1, 0.3, 1] }}
          />
        ))}
      </div>
    </div>
  );
}

/* ─────────────────────────────────────────────
   Card4 – Memory / Knowledge Base Namespaces
   ───────────────────────────────────────────── */

const NS_ICONS: Record<string, React.ElementType> = {
  codebase: Code,
  docs: FileText,
  slack: SlackLogo,
  notion: NotionLogo,
};

const NS_COLORS: Record<string, { bar: string; dot: string; badge: string; buttonBg: string; buttonBorder: string }> = {
  codebase: { bar: "from-violet-500 to-violet-400", dot: "bg-violet-500", badge: "bg-violet-500/15 text-violet-400", buttonBg: "bg-violet-500", buttonBorder: "border-violet-600" },
  docs: { bar: "from-sky-500    to-sky-400", dot: "bg-sky-500", badge: "bg-sky-500/15 text-sky-400", buttonBg: "bg-sky-500", buttonBorder: "border-sky-600" },
  slack: { bar: "from-emerald-500 to-emerald-400", dot: "bg-emerald-500", badge: "bg-emerald-500/15 text-emerald-400", buttonBg: "bg-emerald-500", buttonBorder: "border-emerald-600" },
  notion: { bar: "from-amber-500  to-amber-400", dot: "bg-amber-500", badge: "bg-amber-500/15 text-amber-400", buttonBg: "bg-amber-500", buttonBorder: "border-amber-600" },
};

const RETRIEVAL_QUERIES = [
  { ns: "codebase", q: "vector embeddings auth module", t: "0.2s" },
  { ns: "docs", q: "API rate limiting config", t: "1.1s" },
  { ns: "codebase", q: "Redis cache invalidation patterns", t: "2.4s" },
  { ns: "slack", q: "deployment discussion #eng", t: "4.0s" },
  { ns: "notion", q: "Q3 roadmap — agent features", t: "5.8s" },
  { ns: "docs", q: "OpenAI function calling schema", t: "7.2s" },
];

export function Card4() {
  const namespaces = [
    { name: "codebase", hits: 342, fill: 88 },
    { name: "docs", hits: 218, fill: 56 },
    { name: "slack", hits: 97, fill: 25 },
    { name: "notion", hits: 54, fill: 14 },
  ];

  const [tick, setTick] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setTick((prev) => (prev + 1) % RETRIEVAL_QUERIES.length);
    }, 2000);
    return () => clearInterval(interval);
  }, []);

  const activeNs = RETRIEVAL_QUERIES[tick].ns;
  const recentQueries = [0, 1, 2, 3].map(
    (offset) => RETRIEVAL_QUERIES[(tick - offset + RETRIEVAL_QUERIES.length) % RETRIEVAL_QUERIES.length]
  );

  return (
    <div className="w-full h-full flex gap-5 py-2 px-3">

      {/* ── Left panel: Namespace bars ── */}
      <div className="flex-1 flex flex-col gap-0 min-w-0 pr-2">
        <p className="text-[8px] font-mono uppercase tracking-widest text-muted-foreground mb-3">Namespaces</p>

        <div className="flex flex-col gap-3 flex-1">
          {namespaces.map((ns, i) => {
            const c = NS_COLORS[ns.name];
            const isActive = ns.name === activeNs;
            const Icon = (NS_ICONS[ns.name] || Database) as React.ComponentType<{ size?: number; weight?: string; className?: string }>;

            return (
              <div key={ns.name} className="flex items-center gap-3 group relative">

                {/* Icon Container with 3D effect */}
                <div
                  className={`relative flex shrink-0 items-center justify-center w-[36px] h-[36px] rounded-[12px] border transition-all duration-500 ${isActive ? `shadow-[inset_0_1px_0_0_rgba(255,255,255,0.4),inset_4px_4px_0_0_rgba(255,255,255,0.06),inset_6px_6px_0_0_rgba(255,255,255,0.04),inset_8px_8px_0_0_rgba(255,255,255,0.02),0_1px_2px_0_rgba(0,0,0,0.08),0_2px_4px_0_rgba(0,0,0,0.06),0_4px_6px_0_rgba(0,0,0,0.04),0_6px_8px_0_rgba(0,0,0,0.02)] text-white ${c.buttonBg} ${c.buttonBorder} scale-105` : 'dark:bg-neutral-950/80 shadow-[0_0_0_1px_rgba(0,0,0,0.08),0_1px_2px_-1px_rgba(0,0,0,0.06),0_2px_4px_0px_rgba(0,0,0,0.04)] bg-white border-transparent text-[#A1A1A1]'}`}
                >
                  <Icon size={16} weight={isActive ? "fill" : "regular"} className="relative z-10" />
                </div>

                {/* Name */}
                <span className={`text-[10px] font-mono w-16 shrink-0 transition-colors duration-400 ${isActive ? "text-foreground font-semibold" : "text-muted-foreground group-hover:text-foreground/70"}`}>
                  {ns.name}
                </span>

                {/* Cyberpunk Bar track */}
                <div className="flex-1 h-1.5 bg-muted/30 rounded-full overflow-hidden relative backdrop-blur-sm shadow-inner">
                  <motion.div
                    className={`absolute left-0 top-0 bottom-0 rounded-full overflow-hidden bg-gradient-to-r ${c.bar}`}
                    initial={{ width: "0%" }}
                    animate={{ width: `${ns.fill}%`, opacity: isActive ? 1 : 0.25 }}
                    transition={{
                      width: { duration: 1.2, delay: i * 0.1, type: "spring", bounce: 0.2 },
                      opacity: { duration: 0.4 },
                    }}
                  >
                    {/* Scanning light beam effect inside the bar */}
                    {isActive && (
                      <motion.div
                        className="absolute inset-y-0 left-0 w-full bg-gradient-to-r from-transparent via-white/50 to-transparent"
                        initial={{ x: "-100%" }}
                        animate={{ x: "100%" }}
                        transition={{ repeat: Infinity, duration: 1.5, ease: "linear" }}
                      />
                    )}
                  </motion.div>
                </div>

                {/* Hit count */}
                <div className={`flex items-center gap-1.5 w-10 justify-end transition-all duration-500 ${isActive ? "opacity-100 scale-105" : "opacity-60 scale-100"}`}>
                  <span className={`text-[9px] font-mono font-medium ${isActive ? "text-foreground" : "text-muted-foreground"}`}>
                    {ns.hits}
                  </span>
                  {isActive && (
                    <motion.div
                      className={`w-1 h-1 rounded-full ${c.dot}`}
                      animate={{ opacity: [1, 0.2, 1], scale: [1, 1.5, 1] }}
                      transition={{ repeat: Infinity, duration: 1 }}
                    />
                  )}
                </div>
              </div>
            );
          })}
        </div>

        {/* Live indicator */}
        <div className="flex items-center gap-2 pt-3 mt-auto">
          <div className="relative flex items-center justify-center w-2 h-2">
            <motion.div
              className="absolute inset-0 rounded-full bg-emerald-400/40"
              animate={{ scale: [1, 2.5, 1], opacity: [0.5, 0, 0.5] }}
              transition={{ repeat: Infinity, duration: 2, ease: "easeInOut" }}
            />
            <div className="w-1.5 h-1.5 rounded-full bg-emerald-400" />
          </div>
          <span className="text-[8px] font-mono text-muted-foreground font-medium tracking-wide">Live retrieval active</span>
        </div>
      </div>

      {/* Thin divider */}
      <div className="w-px bg-border/30 self-stretch shrink-0" />

      {/* ── Right panel: Retrieval log ── */}
      <div className="w-[172px] shrink-0 flex flex-col gap-0">
        <p className="text-[8px] font-mono uppercase tracking-widest text-muted-foreground mb-2.5">Retrieval Log</p>

        <div className="flex flex-col gap-1.5 flex-1 overflow-hidden">
          {recentQueries.map((q, qi) => {
            const c = NS_COLORS[q.ns];
            return (
              <motion.div
                key={`${q.ns}-${q.q}-${qi}`}
                className="rounded-xl border border-border/40 bg-muted/20 dark:bg-neutral-950/80 px-2.5 py-2"
                initial={{ opacity: 0, y: -8 }}
                animate={{
                  opacity: qi === 0 ? 1 : qi === 1 ? 0.8 : qi === 2 ? 0.5 : 0.25,
                  y: 0,
                }}
                transition={{ type: "spring", stiffness: 500, damping: 35, delay: qi * 0.05 }}
              >
                <div className="flex items-center gap-1 mb-1">
                  <span className={`text-[6.5px] font-mono font-semibold uppercase px-1.5 py-0.5 rounded-md ${c.badge}`}>
                    {q.ns}
                  </span>
                  <span className="text-[7px] font-mono text-muted-foreground/50 ml-auto tabular-nums">{q.t}</span>
                </div>
                <p className="text-[8px] text-foreground/75 leading-tight font-mono truncate">{q.q}</p>
              </motion.div>
            );
          })}
        </div>
      </div>

    </div>
  );
}

/* ─────────────────────────────────────────────
   Card5 – Tool Call Inspector
───────────────────────────────────────────── */
export function Card5() {
  const tools = [
    { name: "web_search", calls: 14, icon: Globe, latency: "280ms", color: "bg-gradient-to-b from-sky-400 to-sky-600", borderColor: "border-sky-600" },
    { name: "code_exec", calls: 8, icon: TerminalWindow, latency: "1.2s", color: "bg-gradient-to-b from-emerald-400 to-emerald-600", borderColor: "border-emerald-600" },
    { name: "file_read", calls: 22, icon: FileText, latency: "12ms", color: "bg-gradient-to-b from-amber-400 to-amber-600", borderColor: "border-amber-600" },
    { name: "vector_query", calls: 31, icon: Brain, latency: "95ms", color: "bg-gradient-to-b from-violet-400 to-violet-600", borderColor: "border-violet-600" },
  ];

  return (
    <div className="w-full h-full flex items-center justify-center">
      <div className="grid grid-cols-2 gap-2 w-full">
        {tools.map((t, i) => (
          <motion.div
            key={i}
            className="relative rounded-[16px] border border-border/50 bg-background dark:bg-neutral-950/50 shadow-sm hover:shadow-md transition-all duration-300 flex flex-col justify-between p-2.5 group hover:border-border"
            initial={{ opacity: 0, y: 10 }}
            animate={{ opacity: 1, y: 0 }}
            transition={{ delay: i * 0.1, type: "spring", stiffness: 300, damping: 25 }}
          >
            {/* Top Row: 3D Icon + Calls */}
            <div className="flex items-start justify-between">
              <div className={`w-[28px] h-[28px] rounded-[8px] flex items-center justify-center text-white ${t.color} border ${t.borderColor} shadow-[inset_0_0.5px_0_0_rgba(255,255,255,0.6),inset_0.5px_0_0_0_rgba(255,255,255,0.2),inset_0_2px_6px_0_rgba(255,255,255,0.3),inset_0_-0.5px_0_0_rgba(0,0,0,0.3),inset_-0.5px_0_0_0_rgba(0,0,0,0.1),inset_0_-2px_6px_0_rgba(0,0,0,0.1),0_1px_2px_0_rgba(0,0,0,0.08),0_2px_4px_0_rgba(0,0,0,0.06),0_4px_6px_0_rgba(0,0,0,0.04)] group-hover:scale-105 transition-transform duration-300`}>
                <t.icon weight="fill" className="w-3.5 h-3.5 relative z-10" />
              </div>

              <div className="flex flex-col items-end gap-0.5 mt-0.5">
                <span className="text-[12px] font-mono font-bold text-foreground leading-none">{t.calls}</span>
                <span className="text-[7px] font-mono text-muted-foreground/80 uppercase tracking-widest leading-none">Calls</span>
              </div>
            </div>

            {/* Bottom Row: Name + Latency + Progress */}
            <div className="mt-2 flex flex-col gap-1.5">
              <div className="flex items-center justify-between">
                <span className="text-[10px] font-mono font-medium text-foreground tracking-tight">{t.name}</span>
                <span className="text-[8px] font-mono text-muted-foreground tabular-nums">{t.latency}</span>
              </div>
              <div className="w-full h-1.5 bg-muted/50 rounded-full overflow-hidden shadow-inner relative">
                <motion.div
                  className={`absolute left-0 top-0 bottom-0 rounded-full ${t.color}`}
                  initial={{ width: "0%" }}
                  animate={{ width: `${(t.calls / 31) * 100}%` }}
                  transition={{ delay: 0.4 + i * 0.1, duration: 0.8, ease: "easeOut" }}
                />
              </div>
            </div>
          </motion.div>
        ))}
      </div>
    </div>
  );
}

/* ─────────────────────────────────────────────
   Main Grid Component
───────────────────────────────────────────── */
const CARDS = [
  {
    title: "Agent Pipeline",
    description: "Visualise how tasks flow across your multi-agent graph in real time.",
    visual: <Card1 />,
    colSpan: "lg:col-span-1",
    height: "h-[260px]",
  },
  {
    title: "Token Monitor",
    description: "Track LLM token usage and cost-per-run across every model call.",
    visual: <Card2 />,
    colSpan: "lg:col-span-1",
    height: "h-[260px]",
  },
  {
    title: "Activity Feed",
    description: "Real-time logs of agent actions, tool calls, and memory retrievals.",
    visual: <Card3 />,
    colSpan: "lg:col-span-1",
    height: "h-[260px]",
  },
  {
    title: "Knowledge Base",
    description: "Semantic search across documents, codebases, and conversations.",
    visual: <Card4 />,
    colSpan: "lg:col-span-2",
    height: "h-[260px]",
  },
  {
    title: "Tool Inspector",
    description: "Monitor tool usage, latency, and success rates across all agents.",
    visual: <Card5 />,
    colSpan: "lg:col-span-1",
    height: "h-[260px]",
  }
];

export interface AgentBentoGridProps {
  className?: string;
}

export function AgentBentoGrid({ className }: AgentBentoGridProps) {
  return (
    <div className={cn("grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-2 w-full max-w-5xl mx-auto", className)}>
      {CARDS.map((card, idx) => (
        <FeatCard
          key={idx}
          title={card.title}
          description={card.description}
          className={cn(card.colSpan, card.height)}
        >
          {card.visual}
        </FeatCard>
      ))}
    </div>
  );
}

export default AgentBentoGrid;

```

---

## `src\components\ui\alert.tsx`

```tsx
import * as React from "react"
import { cva, type VariantProps } from "class-variance-authority"

import { cn } from "@/lib/utils"

const alertVariants = cva(
  "relative w-full rounded-lg border px-4 py-3 text-sm grid has-[>svg]:grid-cols-[calc(var(--spacing)*4)_1fr] grid-cols-[0_1fr] has-[>svg]:gap-x-3 gap-y-0.5 items-start [&>svg]:size-4 [&>svg]:translate-y-0.5 [&>svg]:text-current",
  {
    variants: {
      variant: {
        default: "bg-card text-card-foreground",
        destructive:
          "text-destructive bg-card [&>svg]:text-current *:data-[slot=alert-description]:text-destructive/90",
      },
    },
    defaultVariants: {
      variant: "default",
    },
  }
)

function Alert({
  className,
  variant,
  ...props
}: React.ComponentProps<"div"> & VariantProps<typeof alertVariants>) {
  return (
    <div
      data-slot="alert"
      role="alert"
      className={cn(alertVariants({ variant }), className)}
      {...props}
    />
  )
}

function AlertTitle({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="alert-title"
      className={cn(
        "col-start-2 line-clamp-1 min-h-4 font-medium tracking-tight",
        className
      )}
      {...props}
    />
  )
}

function AlertDescription({
  className,
  ...props
}: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="alert-description"
      className={cn(
        "text-muted-foreground col-start-2 grid justify-items-start gap-1 text-sm [&_p]:leading-relaxed",
        className
      )}
      {...props}
    />
  )
}

export { Alert, AlertTitle, AlertDescription }

```

---

## `src\components\ui\animated-button.tsx`

```tsx
"use client";

import React from "react";
import { motion, MotionProps } from "framer-motion";
import { cn } from "@/lib/utils";

type AnimatedButtonProps = React.ButtonHTMLAttributes<HTMLButtonElement> &
  MotionProps & {
    children?: React.ReactNode;
    as?: any;
  };

/**
 * AnimatedButton
 * - theme-aware: uses Tailwind `dark:` classes so it works in both light and dark mode
 * - accepts all native button props (onClick, className, type, etc.)
 */
const AnimatedButton: React.FC<AnimatedButtonProps> = ({
  children = "Browse Components",
  className = "",
  as = "button",
  ...rest
}) => {
  const Component = (motion as any)[as] || motion.button;

  return (
    <Component
      {...rest}
      whileHover={{ scale: 1.01 }}
      whileTap={{ scale: 0.97 }}
      transition={{
        type: "spring",
        stiffness: 500,
        damping: 30,
        mass: 0.5,
      }}
      // Set a CSS variable `--shine` that we override for dark mode via Tailwind.
      className={cn(
        "group inline-flex items-center justify-center px-6 py-2 rounded-md relative overflow-hidden bg-neutral-50 dark:bg-black border border-neutral-200 dark:border-[#222]",
        "text-neutral-900 dark:text-neutral-100 font-medium transition-colors focus-visible:outline-none focus-visible:ring-1 focus-visible:ring-neutral-950 disabled:pointer-events-none disabled:opacity-50",
        "[--shine:rgba(0,0,0,.66)] dark:[--shine:rgba(255,255,255,.66)]",
        className,
      )}
    >
      {/* Text with shine mask */}
      <motion.span
        className="tracking-wide font-light flex items-center justify-center h-full w-full relative z-10"
        style={{
          WebkitMaskImage:
            "linear-gradient(-75deg, white calc(var(--mask-x) + 20%), transparent calc(var(--mask-x) + 30%), white calc(var(--mask-x) + 100%))",
          maskImage:
            "linear-gradient(-75deg, white calc(var(--mask-x) + 20%), transparent calc(var(--mask-x) + 30%), white calc(var(--mask-x) + 100%))",
        }}
        initial={{ ["--mask-x" as any]: "100%" } as any}
        animate={{ ["--mask-x" as any]: "-100%" } as any}
        transition={{
          repeat: Infinity,
          duration: 1,
          ease: "linear",
          repeatDelay: 1,
        }}
      >
        {children}
      </motion.span>

      {/* Border shine effect uses the --shine variable so it adapts to theme */}
      <motion.span
        className="block absolute inset-0 rounded-md p-px"
        style={{
          background:
            "linear-gradient(-75deg, transparent 30%, var(--shine) 50%, transparent 70%)",
          backgroundSize: "200% 100%",
          mask: "linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0)",
          maskComposite: "exclude",
          WebkitMask:
            "linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0)",
          WebkitMaskComposite: "xor",
        }}
        initial={{ backgroundPosition: "100% 0", opacity: 0 }}
        animate={{ backgroundPosition: ["100% 0", "0% 0"], opacity: [0, 1, 0] }}
        transition={{
          duration: 1,
          repeat: Infinity,
          ease: "linear",
          repeatDelay: 1,
        }}
      />
    </Component>
  );
};

export default AnimatedButton;

```

---

## `src\components\ui\animated-footer.tsx`

```tsx
"use client";

import * as React from "react";
import { useEffect, useMemo, useRef } from "react";
import gsap from "gsap";
import { useTheme } from "next-themes";
import { cn } from "@/lib/utils";

/**
 * Animated Footer
 *
 * A cinematic, reveal-on-scroll footer: two source images are re-drawn as
 * live ASCII art on <canvas>, light up in little clusters around the cursor,
 * and drift with a soft parallax. When the footer scrolls into view the
 * display headings unmask character-by-character, the links and copy slide
 * up behind masks, and the ASCII "hands" glide in from the edges.
 *
 * Ported from the vanilla "LukeBaffait Animated Footer" (GSAP + canvas) into a
 * single, self-contained, prop-driven React component. No global smooth-scroll
 * or SplitText plugin required — the reveal is driven by an IntersectionObserver
 * and text is split in JSX.
 */

export interface AnimatedFooterLink {
  label: string;
  href: string;
}

export interface AnimatedFooterProps {
  /** The large display words along the bottom edge. Defaults to ["VengeanceUI"]. */
  headingLines?: string[];
  /** Left image URL, sampled into ASCII art. Must be same-origin or CORS-enabled. */
  leftImage?: string;
  /** Right image URL, sampled into ASCII art. Must be same-origin or CORS-enabled. */
  rightImage?: string;

  /** Footer background color. Defaults to "#0f0f0f". */
  background?: string;
  /** Text color for links, copy and headings. Defaults to "#ffffff". */
  textColor?: string;

  /** Character ramp, ordered dark → light, used to render the ASCII art. */
  asciiChars?: string;
  /** Color of the ASCII glyphs. Defaults to "#803500". */
  charColor?: string;
  /** Fill color of a highlighted (hovered) cell. Defaults to "#ff6a00". */
  hoverColor?: string;
  /** Glyph color inside a highlighted cell. Defaults to "#0f0f0f". */
  hoverCharColor?: string;
  /** Number of columns each image is sampled to. Defaults to 80. */
  columns?: number;
  /** Pixel size of each ASCII cell. Defaults to 20. */
  cellSize?: number;
  /** Font size (px) of the ASCII glyphs. Defaults to 18. */
  fontSize?: number;

  /** Pointer parallax strength in px; set to 0 to disable. Defaults to 20. */
  parallaxStrength?: number;
  /** Cursor influence radius, in cells, for the hover highlight. Defaults to 8. */
  hoverRadius?: number;

  /** Play the reveal when the footer scrolls into view (else it shows immediately). Defaults to true. */
  revealOnScroll?: boolean;
  /**
   * Controlled reveal. When set, the footer ignores its own scroll observer and
   * plays in (`true`) / out (`false`) to match this value — drive it from your
   * own ScrollTrigger, sentinel or state to reveal it from behind other content.
   */
  revealed?: boolean;

  /** Extra class names for the root element. */
  className?: string;
}

const DEFAULT_ASCII_CHARS = "........:::=+xX#0369";

const HIGHLIGHT_LIFETIME = 300; // ms a hovered cell stays lit
const CLUSTER_SIZE = 10; // max cells a hover ripple spreads across
const PARALLAX_EASE = 0.05;

interface Cell {
  col: number;
  row: number;
  char: string;
  highlightEndTime: number;
}

interface Hand {
  canvas: HTMLCanvasElement;
  ctx: CanvasRenderingContext2D;
  cells: Map<string, Cell>;
  cellList: Cell[];
  rows: number;
  columns: number;
  cellSize: number;
  baselineOffset: number;
  direction: 1 | -1; // slide-in direction for the reveal curtain
}

/** Build the ASCII cell grid for one image by sampling its brightness. */
function buildHandCells(
  image: HTMLImageElement,
  columns: number,
  asciiChars: string,
): { rows: number; cells: Map<string, Cell> } {
  const rows = Math.max(
    1,
    Math.round(columns / (image.naturalWidth / image.naturalHeight || 1)),
  );

  const sampler = document.createElement("canvas");
  sampler.width = columns;
  sampler.height = rows;
  const sampleCtx = sampler.getContext("2d");
  const cells = new Map<string, Cell>();
  if (!sampleCtx) return { rows, cells };

  sampleCtx.drawImage(image, 0, 0, columns, rows);
  const pixels = sampleCtx.getImageData(0, 0, columns, rows).data;
  const backgroundCharIndex = asciiChars.lastIndexOf(".");

  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < columns; col++) {
      const offset = (row * columns + col) * 4;
      const brightness =
        (pixels[offset] * 0.299 +
          pixels[offset + 1] * 0.587 +
          pixels[offset + 2] * 0.114) /
        255;
      const charIndex = Math.min(
        asciiChars.length - 1,
        Math.floor((1 - brightness) * asciiChars.length),
      );
      if (charIndex <= backgroundCharIndex) continue;

      cells.set(`${col},${row}`, {
        col,
        row,
        char: asciiChars[charIndex],
        highlightEndTime: 0,
      });
    }
  }

  return { rows, cells };
}

/** Light up a wandering cluster of cells starting from `startCell`. */
function highlightCluster(cells: Map<string, Cell>, startCell: Cell) {
  const now = Date.now();
  startCell.highlightEndTime = now + HIGHLIGHT_LIFETIME;

  const steps = Math.floor(Math.random() * CLUSTER_SIZE) + 1;
  const litCells = [startCell];
  let current = startCell;

  for (let step = 0; step < steps; step++) {
    const neighbours: Cell[] = [];
    for (let dy = -1; dy <= 1; dy++) {
      for (let dx = -1; dx <= 1; dx++) {
        if (dx === 0 && dy === 0) continue;
        const neighbour = cells.get(`${current.col + dx},${current.row + dy}`);
        if (neighbour && !litCells.includes(neighbour)) neighbours.push(neighbour);
      }
    }
    if (neighbours.length === 0) break;

    const next = neighbours[Math.floor(Math.random() * neighbours.length)];
    next.highlightEndTime = now + HIGHLIGHT_LIFETIME + step * 10;
    litCells.push(next);
    current = next;
  }
}

/** Nearest scrollable ancestor — used as the reveal's IntersectionObserver root. */
function getScrollParent(node: HTMLElement | null): HTMLElement | null {
  let el = node?.parentElement ?? null;
  while (el) {
    const overflowY = getComputedStyle(el).overflowY;
    if (overflowY === "auto" || overflowY === "scroll" || overflowY === "overlay") return el;
    el = el.parentElement;
  }
  return null;
}

export function AnimatedFooter({
  headingLines = ["VengeanceUI"],
  leftImage = "/animated-footer/hand-left.jpg",
  rightImage = "/animated-footer/hand-right.jpg",
  background,
  textColor,
  charColor,
  hoverColor,
  hoverCharColor,
  asciiChars = DEFAULT_ASCII_CHARS,
  columns = 80,
  cellSize = 20,
  fontSize = 18,
  parallaxStrength = 20,
  hoverRadius = 8,
  revealOnScroll = true,
  revealed,
  className,
}: AnimatedFooterProps) {
  const rootRef = useRef<HTMLElement>(null);
  const leftWrapRef = useRef<HTMLDivElement>(null);
  const rightWrapRef = useRef<HTMLDivElement>(null);
  const leftCanvasRef = useRef<HTMLCanvasElement>(null);
  const rightCanvasRef = useRef<HTMLCanvasElement>(null);

  // Reveal animations, published by the main effect so the controlled-`revealed`
  // effect below can play them without rebuilding the ASCII scene.
  const animateInRef = useRef<() => void>(() => {});
  const animateOutRef = useRef<() => void>(() => {});

  const { resolvedTheme } = useTheme();
  const isDark = resolvedTheme === "dark";

  const cc = charColor ?? (isDark ? "#803500" : "#e6b093");
  const hc = hoverColor ?? "#ff6a00";
  const hcc = hoverCharColor ?? (isDark ? "#0f0f0f" : "#ffffff");

  // Live-tunable values read inside the animation loop, so tweaking a color or
  // the parallax strength never tears down and rebuilds the ASCII scene.
  const liveRef = useRef({ charColor: cc, hoverColor: hc, hoverCharColor: hcc, parallaxStrength, hoverRadius });
  useEffect(() => {
    liveRef.current = { charColor: cc, hoverColor: hc, hoverCharColor: hcc, parallaxStrength, hoverRadius };
  }, [cc, hc, hcc, parallaxStrength, hoverRadius]);

  // A signature of the structural inputs — the scene rebuilds only when one of
  // these changes (images, grid resolution, content, reveal mode).
  const sig = useMemo(
    () =>
      JSON.stringify({
        leftImage,
        rightImage,
        columns,
        cellSize,
        fontSize,
        asciiChars,
        revealOnScroll,
        headingLines,
      }),
    [leftImage, rightImage, columns, cellSize, fontSize, asciiChars, revealOnScroll, headingLines],
  );

  useEffect(() => {
    const root = rootRef.current;
    const leftWrap = leftWrapRef.current;
    const rightWrap = rightWrapRef.current;
    if (!root || !leftWrap || !rightWrap) return;

    const hands: Hand[] = [];
    const wrappers = [leftWrap, rightWrap];

    // ── ASCII hands ──────────────────────────────────────────────────────
    const setupHand = (
      image: HTMLImageElement,
      canvas: HTMLCanvasElement,
      direction: 1 | -1,
    ) => {
      const { rows, cells } = buildHandCells(image, columns, asciiChars);
      if (cells.size === 0) return;

      const dpr = Math.min(window.devicePixelRatio || 1, 2);
      canvas.width = columns * cellSize * dpr;
      canvas.height = rows * cellSize * dpr;

      const ctx = canvas.getContext("2d");
      if (!ctx) return;
      ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
      ctx.font = `${fontSize}px monospace`;
      ctx.textAlign = "center";
      ctx.textBaseline = "alphabetic";

      const metrics = ctx.measureText("X");
      const glyphHeight = metrics.actualBoundingBoxAscent + metrics.actualBoundingBoxDescent;
      const baselineOffset = cellSize / 2 + glyphHeight / 2 - metrics.actualBoundingBoxDescent;

      hands.push({
        canvas,
        ctx,
        cells,
        cellList: [...cells.values()],
        rows,
        columns,
        cellSize,
        baselineOffset,
        direction,
      });
    };

    const loadHand = (src: string, canvas: HTMLCanvasElement, direction: 1 | -1) => {
      if (!src) return;
      const image = new Image();
      image.crossOrigin = "anonymous";
      let initialized = false;
      const init = () => {
        if (initialized) return;
        initialized = true;
        setupHand(image, canvas, direction);
      };
      image.onload = init;
      image.src = src;
      if (image.complete && image.naturalWidth) init();
    };
    loadHand(leftImage, leftCanvasRef.current!, 1);
    loadHand(rightImage, rightCanvasRef.current!, -1);

    const renderHand = (hand: Hand, now: number) => {
      const { ctx, cellList, cellSize: cs, baselineOffset, columns: cols, rows } = hand;
      const { charColor: cc, hoverColor: hc, hoverCharColor: hcc } = liveRef.current;
      ctx.clearRect(0, 0, cols * cs, rows * cs);

      for (const cell of cellList) {
        const x = cell.col * cs;
        const y = cell.row * cs;
        const isHighlighted = cell.highlightEndTime > now;

        if (isHighlighted) {
          ctx.fillStyle = hc;
          ctx.fillRect(x, y, cs, cs);
        }
        ctx.fillStyle = isHighlighted ? hcc : cc;
        ctx.fillText(cell.char, x + cs / 2, y + baselineOffset);
      }
    };

    // ── Pointer: hover highlight + parallax target ───────────────────────
    const pointer = { x: 0, y: 0 };
    const drift = { x: 0, y: 0 };
    // Reveal "curtain": hands start pushed off the edges and slide to 0.
    const curtain = { offset: revealOnScroll ? 125 : 0 };

    const hoverHand = (hand: Hand, clientX: number, clientY: number) => {
      const rect = hand.canvas.getBoundingClientRect();
      if (rect.width === 0 || rect.height === 0) return;
      const mouseCol = ((clientX - rect.left) / rect.width) * hand.columns;
      const mouseRow = ((clientY - rect.top) / rect.height) * hand.rows;

      let closest: Cell | null = null;
      let closestDist = Infinity;
      for (const cell of hand.cellList) {
        const dx = mouseCol - cell.col;
        const dy = mouseRow - cell.row;
        const dist = Math.sqrt(dx * dx + dy * dy);
        if (dist < closestDist) {
          closestDist = dist;
          closest = cell;
        }
      }
      if (closest && closestDist <= liveRef.current.hoverRadius) {
        highlightCluster(hand.cells, closest);
      }
    };

    const onMouseMove = (event: MouseEvent) => {
      const strength = liveRef.current.parallaxStrength;
      const rect = root.getBoundingClientRect();
      const w = rect.width || 1;
      const h = rect.height || 1;
      pointer.x = ((event.clientX - rect.left) / w - 0.5) * strength * 2;
      pointer.y = ((event.clientY - rect.top) / h - 0.5) * strength * 2;
      for (const hand of hands) hoverHand(hand, event.clientX, event.clientY);
    };
    window.addEventListener("mousemove", onMouseMove);

    // ── Unified render loop: ASCII + parallax + reveal curtain ───────────
    let rafId = 0;
    const frame = () => {
      const now = Date.now();
      for (const hand of hands) renderHand(hand, now);

      drift.x += (pointer.x - drift.x) * PARALLAX_EASE;
      drift.y += (pointer.y - drift.y) * PARALLAX_EASE;
      const strength = liveRef.current.parallaxStrength;
      const scale = 1 + (strength * 2) / 200;

      wrappers.forEach((wrapper, i) => {
        const dir = i === 0 ? 1 : -1;
        const revealX = i === 0 ? -curtain.offset : curtain.offset;
        const x = drift.x * dir || 0;
        const y = -drift.y || 0;
        // Apply reveal via translateX, then apply parallax via translate, avoiding calc() mixed-unit bugs
        wrapper.style.transform = `translateX(${revealX}%) translate(${x}px, ${y}px) scale(${scale})`;
      });

      rafId = requestAnimationFrame(frame);
    };
    rafId = requestAnimationFrame(frame);

    // ── Reveal (chars + curtain) ─────────────────────────
    const chars = gsap.utils.toArray<HTMLElement>(root.querySelectorAll("[data-af-char]"));

    const animateIn = () => {
      gsap.to(curtain, { offset: 0, duration: 1, ease: "power3.out", overwrite: true });
      gsap.to(chars, {
        yPercent: 0,
        duration: 1,
        ease: "power3.out",
        stagger: { each: 0.04, from: "center" },
        overwrite: true,
      });
    };

    const animateOut = () => {
      gsap.to(curtain, { offset: 125, duration: 0.4, ease: "power2.in", overwrite: true });
      gsap.to(chars, {
        yPercent: 125,
        duration: 0.4,
        ease: "power2.in",
        stagger: { each: 0.01, from: "center" },
        overwrite: true,
      });
    };

    // Publish for the controlled-`revealed` effect.
    animateInRef.current = animateIn;
    animateOutRef.current = animateOut;

    const maskAll = () => {
      gsap.set(chars, { yPercent: 125 });
    };
    const showAll = () => {
      gsap.set(chars, { yPercent: 0 });
    };

    let observer: IntersectionObserver | null = null;

    if (revealed !== undefined) {
      // Controlled: the `revealed` effect below drives the reveal. Set the
      // initial state to match, and never attach the scroll observer.
      curtain.offset = revealed ? 0 : 125;
      if (revealed) showAll();
      else maskAll();
    } else if (revealOnScroll) {
      // Start fully masked — nothing shows until the footer is scrolled into view.
      maskAll();

      // Drive the reveal purely from scroll position, relative to the nearest
      // scrollable ancestor (the page in real use, or the preview's scroll
      // container in the docs). Plays in when the footer crosses into view and
      // reverses when you scroll back up.
      let isRevealed = false;
      observer = new IntersectionObserver(
        (entries) => {
          for (const entry of entries) {
            if (entry.isIntersecting && !isRevealed) {
              isRevealed = true;
              animateIn();
            } else if (!entry.isIntersecting && isRevealed) {
              isRevealed = false;
              animateOut();
            }
          }
        },
        { root: getScrollParent(root), threshold: 0.35 },
      );
      observer.observe(root);
    } else {
      showAll();
    }

    // ── Cleanup ──────────────────────────────────────────────────────────
    return () => {
      cancelAnimationFrame(rafId);
      window.removeEventListener("mousemove", onMouseMove);
      observer?.disconnect();
      gsap.killTweensOf([curtain, ...chars]);
    };
    // Rebuild only when a structural input changes; live values flow via liveRef.
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [sig]);

  // Controlled reveal: play in/out to match the `revealed` prop without
  // rebuilding the scene. Ignored entirely when `revealed` is undefined.
  useEffect(() => {
    if (revealed === undefined) return;
    if (revealed) animateInRef.current();
    else animateOutRef.current();
  }, [revealed]);

  // Whether the content starts masked on first paint (avoids a flash before the
  // effect runs): hidden unless it's meant to be shown immediately.
  const startsHidden = revealed !== undefined ? !revealed : revealOnScroll;
  const offEdge = startsHidden ? 125 : 0;

  return (
    <footer
      ref={rootRef}
      className={cn(
        "relative h-full w-full overflow-hidden",
        !background && "bg-white dark:bg-black",
        !textColor && "text-black dark:text-white",
        className
      )}
      style={{ backgroundColor: background, color: textColor, containerType: "inline-size" }}
    >
      {/* ASCII hands */}
      <div className="pointer-events-none absolute inset-0 flex items-center justify-between">
        <div
          ref={leftWrapRef}
          className="relative w-2/5 min-w-[200px] will-change-transform"
          style={{ transform: `translateX(-${offEdge}%)` }}
        >
          <canvas ref={leftCanvasRef} className="block h-auto w-full" />
        </div>
        <div
          ref={rightWrapRef}
          className="relative w-2/5 min-w-[200px] will-change-transform"
          style={{ transform: `translateX(${offEdge}%)` }}
        >
          <canvas ref={rightCanvasRef} className="block h-auto w-full" />
        </div>
      </div>

      {/* Display headings */}
      <div className="absolute inset-x-0 bottom-0 flex items-end justify-center gap-4 p-8">
        {headingLines.map((word, wi) => (
          <h2
            key={`${word}-${wi}`}
            aria-label={word}
            className="overflow-hidden font-medium leading-none tracking-tight pb-[0.15em] -mb-[0.15em]"
            style={{ fontSize: "clamp(2rem, 13cqw, 11rem)" }}
          >
            {Array.from(word).map((ch, ci) => (
              <span
                key={ci}
                data-af-char
                aria-hidden="true"
                className="inline-block"
              >
                {ch === " " ? " " : ch}
              </span>
            ))}
          </h2>
        ))}
      </div>
    </footer>
  );
}

export default AnimatedFooter;

```

---

## `src\components\ui\animated-number.tsx`

```tsx
"use client"

import React, { useEffect, useRef, useState } from 'react'
import { motion, AnimatePresence } from "framer-motion"
import { cn } from "@/lib/utils"

function AnimatedNumber({ value, className }: { value: number, className?: string }) {
    return (
        <div className={cn("flex items-center", className)}>
            <div className="flex relative items-center">
                {value.toString().split("").map((digit, index) => (
                    <SingleNumberHolder key={index} value={digit} index={index} />
                ))}
            </div>
        </div>
    )
}

function SingleNumberHolder({ value, index }: { value: string, index: number }) {
    const [height, setHeight] = useState<string | null>(null)
    const containerRef = useRef<HTMLDivElement>(null)
    let notANumber = false

    useEffect(() => {
        if (containerRef.current) {
            setHeight(getComputedStyle(containerRef.current).height)
        }
    }, [])

    if (index === 0) {
        notANumber = isNaN(Number.parseInt(value))
    }

    const vars = {
        init: { opacity: 0 },
        animate: { opacity: 1 },
        exit: { opacity: 0 },
    }

    return (
        <div
            className="relative"
            style={{ height: height || "auto", overflowY: "hidden", overflowX: "clip" }}
            ref={containerRef}
        >
            {notANumber && (
                <motion.span
                    initial="init"
                    animate="animate"
                    exit="exit"
                    variants={vars}
                    key={value}
                    layout="size"
                >
                    {value}
                </motion.span>
            )}
            {!notANumber && <RenderStrip value={value} eleHeight={height} />}
        </div>
    )
}

const zeroToNine = Array.from({ length: 10 }, (_, k) => k)

function RenderStrip({ eleHeight, value }: { eleHeight: string | null, value: string }) {
    const heightInNumber = Number.parseInt(eleHeight?.replace("px", "") || "48")
    const negative = heightInNumber * -1
    const pos = heightInNumber
    const prev = useRef(value)

    // Convert string values to numbers for comparison
    const currentVal = parseInt(value)
    const prevVal = parseInt(prev.current)

    // Calculate direction based on value change
    const diff = prevVal - currentVal
    const dir = currentVal > prevVal ? pos * diff * -1 : negative * diff

    // Update ref after calculation
    useEffect(() => {
        prev.current = value
    }, [value])

    return (
        <AnimatePresence mode='wait'>
            <motion.div
                key={value}
                initial={{ y: dir }}
                animate={{ y: 0 }}
                exit={{ y: 0, transition: { duration: 0.1 } }}
                transition={{ duration: 0.5, ease: "easeOut" }}
                className='flex relative flex-col'
            >
                {/* Numbers smaller than current */}
                <motion.span
                    layout
                    key={`negative-${value}`}
                    className={cn('flex flex-col items-center absolute bottom-full left-0')}
                >
                    {zeroToNine.filter(val => val < currentVal).map((val, idx) => (
                        <span key={`${val}_${idx}`}>{val}</span>
                    ))}
                </motion.span>

                {/* Current Number */}
                <span key={`current-${value}`}>{value}</span>

                {/* Numbers larger than current */}
                <motion.span
                    layout
                    key={`positive-${value}`}
                    className={cn('flex flex-col items-center absolute top-full left-0')}
                >
                    {zeroToNine.filter(val => val > currentVal).map((val, idx) => (
                        <span key={`${val}_${idx}`}>{val}</span>
                    ))}
                </motion.span>
            </motion.div>
        </AnimatePresence>
    )
}

// Score-style animated number with color feedback
function AnimatedScore({ value, duration = 0.2, className }: { value: number, duration?: number, className?: string }) {
    const prevValueRef = useRef(value)

    useEffect(() => {
        prevValueRef.current = value
    }, [value])

    const colors = {
        negative: "#37ff1a",
        positive: "#ff1a4b",
        neutral: "#fff"
    }

    const transforVal = 80
    const forwards = {
        init: { y: transforVal * -1, opacity: 0, scale: 0.5, color: colors.negative },
        animate: {
            y: 0,
            opacity: 1,
            scale: [1.7, 1],
            color: [colors.negative, colors.negative, colors.neutral],
            transition: { duration: 0.4, times: [0, 0.7, 1], color: { times: [0, 0.75, 0.9] } },
        },
        exit: {
            y: transforVal,
            opacity: 0,
            scale: 0.5,
            color: colors.positive
        },
    }

    const backwards = {
        init: { y: transforVal, opacity: 0, scale: 0.5, color: colors.positive },
        animate: {
            y: 0,
            opacity: 1,
            scale: [1.7, 1],
            color: [colors.positive, colors.positive, colors.neutral],
            transition: { duration: 0.4, times: [0, 0.7, 1], color: { times: [0, 0.75, 0.9] } },
        },
        exit: {
            y: transforVal * -1,
            opacity: 0,
            scale: 0.5,
            color: colors.negative
        }
    }

    const variants = value >= prevValueRef.current ? forwards : backwards
    const direction = value >= prevValueRef.current ? "forwards" : "backwards"

    return (
        <div className={cn("relative flex justify-center items-center py-1 px-2 w-full rounded-md", className)}>
            <motion.div layout="size" className='w-fit flex justify-center items-center'>
                {value.toString().split("").map((number, index) => (
                    <ScoreContainer
                        direction={direction}
                        duration={duration}
                        variants={variants}
                        number={number}
                        key={index}
                    />
                ))}
            </motion.div>
        </div>
    )
}

function ScoreContainer({ number, variants, duration = 0.7, direction }: {
    number: string,
    variants: any,
    duration?: number,
    direction: string
}) {
    const cached = React.useMemo(() => (
        <div className='relative'>
            <AnimatePresence mode='popLayout'>
                <motion.div
                    animate="animate"
                    className='flex justify-center items-center'
                    initial="init"
                    exit="exit"
                    variants={variants}
                    key={number.toString()}
                    layout="size"
                    transition={{ duration, ease: "backInOut" }}
                >
                    {number}
                </motion.div>
            </AnimatePresence>
        </div>
    ), [number, direction, variants, duration])

    return <React.Fragment>{cached}</React.Fragment>
}

export { AnimatedNumber, AnimatedScore }

```

---

## `src\components\ui\animated-rays.tsx`

```tsx
"use client";

import { useEffect, useState } from "react";
import { cn } from "@/lib/utils";

interface AnimatedRaysProps {
    /** Additional CSS classes */
    className?: string;
    /** Optional children to render over the background */
    children?: React.ReactNode;
}

export function AnimatedRays({
    className = "",
    children,
}: AnimatedRaysProps) {
    const [isDark, setIsDark] = useState(false);
    const [mounted, setMounted] = useState(false);

    useEffect(() => {
        setMounted(true);
        const checkDark = () => document.documentElement.classList.contains("dark");
        setIsDark(checkDark());

        const observer = new MutationObserver(() => setIsDark(checkDark()));
        observer.observe(document.documentElement, {
            attributes: true,
            attributeFilter: ["class"],
        });
        return () => observer.disconnect();
    }, []);

    if (!mounted) return null;

    const stripes = `repeating-linear-gradient(
        100deg,
        var(--stripe-color) 0%,
        var(--stripe-color) 7%,
        transparent 10%,
        transparent 12%,
        var(--stripe-color) 16%
    )`;
    const rainbow = `repeating-linear-gradient(
        100deg,
        #60a5fa 10%,
        #e879f9 15%,
        #60a5fa 20%,
        #5eead4 25%,
        #60a5fa 30%
    )`;

    return (
        <section className={cn("relative w-full h-full overflow-hidden", className)}>
            {/* Aurora Background — matches original .hero */}
            <div
                className="absolute inset-0"
                style={{
                    backgroundImage: `${stripes}, ${rainbow}`,
                    backgroundSize: "300%, 200%",
                    backgroundPosition: "50% 50%, 50% 50%",
                    filter: isDark
                        ? "blur(10px) opacity(50%) saturate(200%)"
                        : "blur(10px) invert(100%)",
                    maskImage: "radial-gradient(ellipse at 100% 0%, black 40%, transparent 70%)",
                    WebkitMaskImage: "radial-gradient(ellipse at 100% 0%, black 40%, transparent 70%)",
                }}
            >
                {/* Animated overlay — matches original .hero::after */}
                <div
                    className="absolute inset-0 animate-aurora-bg"
                    style={{
                        backgroundImage: `${stripes}, ${rainbow}`,
                        backgroundSize: "200%, 100%",
                        backgroundAttachment: "fixed",
                        mixBlendMode: "difference",
                    }}
                />
            </div>

            {children && (
                <div className="absolute inset-0 z-10 flex flex-col items-center justify-center">
                    {children}
                </div>
            )}
        </section>
    );
}

export default AnimatedRays;

```

---

## `src\components\ui\animated-tooltip.tsx`

```tsx
"use client";

import * as React from "react";
import { useState, useId } from "react";
import { AnimatePresence, motion, type Variants } from "framer-motion";
import { cn } from "@/lib/utils";

/**
 * Animated Tooltip
 * Bouncy, playful tooltip shapes with SVG bubbles and spring animations.
 * Inspired by Codrops' "Tooltip Animations" (originally anime.js), rebuilt
 * on framer-motion with theme-adaptive colors.
 */

export type AnimatedTooltipVariant =
  | "cora"
  | "smaug"
  | "dori"
  | "gram"
  | "indis"
  | "malva"
  | "sadoc";

interface VariantConfig {
  /** Bubble size in px. */
  width: number;
  height: number;
  /** Distance of the bubble above the trigger (CSS `bottom`). */
  bottom: string;
  /** transform-origin for the entrance animation. */
  transformOrigin: string;
  /** SVG path(s) rendered inside a `0 0 400 300` viewBox. */
  shape: (fill: string) => React.ReactNode;
  /** Bubble (base) entrance/exit animation, transitions embedded per state. */
  base: Variants;
  /** Inner content entrance/exit animation, transitions embedded per state. */
  content: Variants;
  /** Extra styles for the content wrapper. */
  contentStyle?: React.CSSProperties;
}

const EASE_OUT_QUINT: [number, number, number, number] = [0.22, 1, 0.36, 1];
const EASE_IN: [number, number, number, number] = [0.55, 0, 1, 0.45];

const VARIANTS: Record<AnimatedTooltipVariant, VariantConfig> = {
  // Blobby heart — scales up while un-rotating.
  cora: {
    width: 232,
    height: 174,
    bottom: "calc(100% + 0.5rem)",
    transformOrigin: "50% 100%",
    shape: (fill) => (
      <path
        d="M 199,21.9 C 152,22.2 109,35.7 78.8,57.4 48,79.1 29,109 29,142 29,172 45.9,201 73.6,222 101,243 140,258 183,260 189,270 200,282 200,282 200,282 211,270 217,260 261,258 299,243 327,222 354,201 371,172 371,142 371,109 352,78.7 321,57 290,35.3 247,21.9 199,21.9 Z"
        fill={fill}
      />
    ),
    base: {
      initial: { scale: 0, rotate: -180, opacity: 0 },
      animate: { scale: 1, rotate: 0, opacity: 1, transition: { duration: 0.6, ease: EASE_OUT_QUINT } },
      exit: { scale: 0, opacity: 0, transition: { duration: 0.18, ease: EASE_IN } },
    },
    content: {
      initial: { y: 20, opacity: 0 },
      animate: { y: 0, opacity: 1, transition: { duration: 0.3, delay: 0.25, ease: EASE_OUT_QUINT } },
      exit: { y: 20, opacity: 0, transition: { duration: 0.1, ease: EASE_IN } },
    },
    contentStyle: { marginBottom: "0.75em" },
  },

  // Rounded pill with a downward pointer — tips in with a slide.
  smaug: {
    width: 240,
    height: 180,
    bottom: "calc(100% - 0.25rem)",
    transformOrigin: "50% 100%",
    shape: (fill) => (
      <path
        d="M 314,100 C 313,100 312,100 311,100 L 89.5,100 C 55.9,100 29.1,121 29.1,150 29.1,178 53.1,201 89.5,201 L 184,201 200,223 217,201 311,201 C 344,201 371,178 371,150 371,122 346,99 314,100 Z"
        fill={fill}
      />
    ),
    base: {
      initial: { rotate: 35, opacity: 0 },
      animate: { rotate: 0, opacity: 1, transition: { duration: 0.2, ease: "easeOut" } },
      exit: { rotate: -35, opacity: 0, transition: { duration: 0.2, ease: EASE_IN } },
    },
    content: {
      initial: { x: 50, rotate: 6, opacity: 0 },
      animate: { x: 0, rotate: 0, opacity: 1, transition: { type: "spring", stiffness: 260, damping: 12, delay: 0.05 } },
      exit: { x: -30, rotate: -6, opacity: 0, transition: { duration: 0.2, ease: EASE_IN } },
    },
  },

  // Banner ribbon with a pointer — drops in with elastic bounce.
  dori: {
    width: 240,
    height: 180,
    bottom: "calc(100% - 0.25rem)",
    transformOrigin: "50% 0%",
    shape: (fill) => (
      <path
        d="M 22,108 22,236 C 22,236 64,216 103,212 142,208 184,212 184,212 L 200,229 216,212 C 216,212 258,207 297,212 336,217 378,236 378,236 L 378,108 C 378,108 318,83.7 200,83.7 82,83.7 22,108 22,108 Z"
        fill={fill}
      />
    ),
    base: {
      initial: { y: -60, scale: 0.5, opacity: 0 },
      animate: { y: 0, scale: 1, opacity: 1, transition: { type: "spring", bounce: 0.5, duration: 0.8 } },
      exit: { y: -60, scale: 0.5, opacity: 0, transition: { duration: 0.2, ease: EASE_IN } },
    },
    content: {
      initial: { y: 20, opacity: 0 },
      animate: { y: 0, opacity: 1, transition: { duration: 0.3, delay: 0.1, ease: EASE_OUT_QUINT } },
      exit: { y: 20, opacity: 0, transition: { duration: 0.1, ease: EASE_IN } },
    },
    contentStyle: { marginBottom: "0.75em" },
  },

  // Wavy blob — squashes horizontally on entry.
  gram: {
    width: 240,
    height: 180,
    bottom: "calc(100% - 0.25rem)",
    transformOrigin: "50% 100%",
    shape: (fill) => (
      <path
        d="M 92.4,79 C 136,79 154,115 200,116 246,117 263,80.4 308,79 353,77.6 381,111 381,150 381,189 346,220 308,221 270,222 236,188 200,188 164,188 130,222 92.4,221 54.4,220 19,189 19,150 19,111 48.6,79 92.4,79 Z"
        fill={fill}
      />
    ),
    base: {
      initial: { scaleX: 1.2, opacity: 0 },
      animate: { scaleX: 1, opacity: 1, transition: { duration: 0.4, ease: EASE_OUT_QUINT } },
      exit: { scaleX: 1.1, scaleY: 0.9, opacity: 0, transition: { duration: 0.2, ease: EASE_IN } },
    },
    content: {
      initial: { scale: 0.8, opacity: 0 },
      animate: { scale: 1, opacity: 1, transition: { duration: 0.3, delay: 0.1, ease: EASE_OUT_QUINT } },
      exit: { scale: 0.8, opacity: 0, transition: { duration: 0.15, ease: EASE_IN } },
    },
  },

  // Rounded rectangle — rises from below with a spring.
  indis: {
    width: 240,
    height: 180,
    bottom: "calc(100% + 0.25rem)",
    transformOrigin: "50% 100%",
    shape: (fill) => (
      <path
        d="M 44.5,24 C 148,24 252,24 356,24 367,24 376,32.9 376,44 L 376,256 C 376,267 367,276 356,276 252,276 148,276 44.5,276 33.4,276 24.5,267 24.5,256 L 24.5,44 C 24.5,32.9 33.4,24 44.5,24 Z"
        fill={fill}
      />
    ),
    base: {
      initial: { y: 100, scaleX: 0.3, scaleY: 1.3, opacity: 0 },
      animate: { y: 0, scaleX: 1, scaleY: 1, opacity: 1, transition: { type: "spring", bounce: 0.45, duration: 0.9 } },
      exit: { y: 100, scaleX: 0, scaleY: 1.5, opacity: 0, transition: { duration: 0.25, ease: EASE_IN } },
    },
    content: {
      initial: { y: 10, opacity: 0 },
      animate: { y: 0, opacity: 1, transition: { duration: 0.3, delay: 0.08, ease: "easeOut" } },
      exit: { y: -20, opacity: 0, transition: { duration: 0.15, ease: EASE_IN } },
    },
  },

  // Spiky starburst — pops in with a jiggle.
  malva: {
    width: 220,
    height: 165,
    bottom: "calc(100% + 0.25rem)",
    transformOrigin: "50% 100%",
    shape: (fill) => (
      <path
        d="M 94.9,90.2 101,30.7 163,72.3 229,17.7 263,68.2 319,55.9 315,102 375,144 316,175 340,228 265,220 251,263 180,233 143,282 98.9,218 57.5,236 82,189 25,170 82.8,141 48.7,93.7 Z"
        fill={fill}
      />
    ),
    base: {
      initial: { scale: 0, rotate: -20, opacity: 0 },
      animate: { scale: [0, 1.15, 1], rotate: [-20, 6, 0], opacity: 1, transition: { duration: 0.7, ease: EASE_OUT_QUINT, times: [0, 0.6, 1] } },
      exit: { scale: 0, opacity: 0, transition: { duration: 0.18, ease: EASE_IN } },
    },
    content: {
      initial: { scale: 0.7, opacity: 0 },
      animate: { scale: 1, opacity: 1, transition: { duration: 0.3, delay: 0.25, ease: EASE_OUT_QUINT } },
      exit: { scale: 0.7, opacity: 0, transition: { duration: 0.1, ease: EASE_IN } },
    },
    contentStyle: { width: "55%" },
  },

  // Outlined speech bubble — springs up from the trigger.
  sadoc: {
    width: 240,
    height: 180,
    bottom: "calc(100% + 0.5rem)",
    transformOrigin: "50% 100%",
    shape: (fill) => (
      <path
        d="M 32.1,42.7 54.5,257 185,257 193,269 200,282 207,269 214,257 342,257 368,23.9 Z"
        fill={fill}
        stroke="var(--muted-foreground)"
        strokeWidth={3}
        strokeLinejoin="round"
      />
    ),
    base: {
      initial: { y: -40, opacity: 0 },
      animate: { y: 0, opacity: 1, transition: { type: "spring", bounce: 0.5, duration: 0.8 } },
      exit: { y: -40, opacity: 0, transition: { duration: 0.2, ease: EASE_IN } },
    },
    content: {
      initial: { y: 20, opacity: 0 },
      animate: { y: 0, opacity: 1, transition: { type: "spring", bounce: 0.4, duration: 0.8, delay: 0.2 } },
      exit: { y: 20, opacity: 0, transition: { duration: 0.15, ease: EASE_IN } },
    },
    contentStyle: { marginBottom: "1.25em" },
  },
};

export interface AnimatedTooltipProps {
  /** The trigger label (usually a word or short phrase). */
  children: React.ReactNode;
  /** The tooltip body content revealed on hover/focus. */
  content: React.ReactNode;
  /** Which shape + animation style to use. Defaults to "cora". */
  variant?: AnimatedTooltipVariant;
  /** Trigger text color while the tooltip is open. Defaults to a soft green. */
  accentColor?: string;
  /** Bubble fill color. Defaults to the theme foreground token. */
  shapeColor?: string;
  /** Tooltip text color. Defaults to the theme background token. */
  textColor?: string;
  /** Additional classes for the inline wrapper. */
  className?: string;
}

export function AnimatedTooltip({
  children,
  content,
  variant = "cora",
  accentColor = "#6fbb95",
  shapeColor = "var(--foreground)",
  textColor = "var(--background)",
  className,
}: AnimatedTooltipProps) {
  const [open, setOpen] = useState(false);
  const cfg = VARIANTS[variant] ?? VARIANTS.cora;
  const id = useId().replace(/:/g, "");

  return (
    <span
      className={cn("relative inline-block", className)}
      onMouseEnter={() => setOpen(true)}
      onMouseLeave={() => setOpen(false)}
      onFocus={() => setOpen(true)}
      onBlur={() => setOpen(false)}
    >
      <motion.span
        role="button"
        tabIndex={0}
        aria-describedby={open ? id : undefined}
        className="cursor-pointer select-none inline-block px-3 py-2 font-medium"
        animate={{ color: open ? accentColor : "var(--foreground)" }}
        transition={{ duration: 0.3, ease: "easeOut" }}
      >
        {children}
      </motion.span>

      {/* Static anchor keeps the bubble centered above the trigger;
          the inner motion element only handles the entrance transforms. */}
      <span
        aria-hidden={!open}
        style={{
          position: "absolute",
          bottom: cfg.bottom,
          left: "50%",
          width: cfg.width,
          height: cfg.height,
          marginLeft: -cfg.width / 2,
          pointerEvents: "none",
          zIndex: 50,
        }}
      >
        <AnimatePresence>
          {open && (
            <motion.span
              key="base"
              id={id}
              role="tooltip"
              variants={cfg.base}
              initial="initial"
              animate="animate"
              exit="exit"
              style={{
                position: "absolute",
                inset: 0,
                display: "flex",
                alignItems: "center",
                justifyContent: "center",
                transformOrigin: cfg.transformOrigin,
              }}
            >
              <svg
                viewBox="0 0 400 300"
                preserveAspectRatio="xMidYMid meet"
                style={{ position: "absolute", inset: 0, width: "100%", height: "100%" }}
                aria-hidden="true"
              >
                {cfg.shape(shapeColor)}
              </svg>
              <motion.span
                variants={cfg.content}
                initial="initial"
                animate="animate"
                exit="exit"
                style={{
                  position: "relative",
                  width: "65%",
                  textAlign: "center",
                  fontSize: "0.85rem",
                  lineHeight: 1.4,
                  color: textColor,
                  ...cfg.contentStyle,
                }}
              >
                {content}
              </motion.span>
            </motion.span>
          )}
        </AnimatePresence>
      </span>
    </span>
  );
}

export default AnimatedTooltip;

```

---

## `src\components\ui\ascii-glitch-ripple.tsx`

```tsx
"use client";

import React, { useEffect, useRef } from "react";
import { cn } from "@/lib/utils";

// Constants for wave animation behavior
const WAVE_THRESH = 3;
const CHAR_MULT = 3;
const ANIM_STEP = 40;
const WAVE_BUF = 5;

export interface AsciiGlitchRippleProps extends React.AnchorHTMLAttributes<HTMLAnchorElement> {
  /**
   * The text to display and animate.
   */
  children: string;
  /**
   * The HTML element or component to render as.
   * @default "a"
   */
  as?: any;
  /**
   * Additional CSS classes.
   */
  className?: string;
  /**
   * Duration of each ripple wave in milliseconds.
   * @default 1000
   */
  dur?: number;
  /**
   * Character set to scramble through during the ripple wave.
   * @default '.,·-─~+:;=*π""┐┌┘┴┬╗╔╝╚╬╠╣╩╦║░▒▓█▄▀▌▐■!?&#$@0123456789*'
   */
  chars?: string;
  /**
   * Whether to preserve space characters or scramble them too.
   * @default true
   */
  preserveSpaces?: boolean;
  /**
   * The spread of the ripple wave. Larger numbers mean wider waves.
   * @default 1.0
   */
  spread?: number;
  [key: string]: any;
}

export function AsciiGlitchRipple({
  children,
  as = "a",
  className,
  dur = 1000,
  chars = '.,·-─~+:;=*π""┐┌┘┴┬╗╔╝╚╬╠╣╩╦║░▒▓█▄▀▌▐■!?&#$@0123456789*',
  preserveSpaces = true,
  spread = 1.0,
  ...props
}: AsciiGlitchRippleProps) {
  const Component = as;
  const elRef = useRef<any>(null);

  // Use a mutable ref to store animation state, preventing unnecessary React renders
  const stateRef = useRef({
    origTxt: children,
    origChars: children.split(""),
    isAnim: false,
    cursorPos: 0,
    waves: [] as Array<{ startPos: number; startTime: number; id: number }>,
    animId: null as number | null,
    isHover: false,
    origW: null as number | null,
    dur,
    chars,
    preserveSpaces,
    spread,
  });

  // Keep internal mutable state updated when props change
  useEffect(() => {
    stateRef.current.origTxt = children;
    stateRef.current.origChars = children.split("");
    stateRef.current.dur = dur;
    stateRef.current.chars = chars;
    stateRef.current.preserveSpaces = preserveSpaces;
    stateRef.current.spread = spread;

    // Reset layout widths if text changes dynamically
    if (stateRef.current.origW !== null && elRef.current) {
      elRef.current.style.width = "";
      stateRef.current.origW = null;
    }

    if (!stateRef.current.isAnim && elRef.current) {
      elRef.current.textContent = children;
    }
  }, [children, dur, chars, preserveSpaces, spread]);

  useEffect(() => {
    const el = elRef.current;
    if (!el) return;

    // Initialize content
    el.textContent = children;

    const updateCursorPos = (e: MouseEvent) => {
      const rect = el.getBoundingClientRect();
      const x = e.clientX - rect.left;
      const len = stateRef.current.origTxt.length;
      const pos = Math.round((x / rect.width) * len);
      stateRef.current.cursorPos = Math.max(0, Math.min(pos, len - 1));
    };

    const stop = () => {
      el.textContent = stateRef.current.origTxt;
      el.classList.remove("as");

      // Restore natural width layout
      if (stateRef.current.origW !== null) {
        el.style.width = "";
        stateRef.current.origW = null;
      }
      stateRef.current.isAnim = false;
      if (stateRef.current.animId) {
        cancelAnimationFrame(stateRef.current.animId);
        stateRef.current.animId = null;
      }
    };

    const start = () => {
      if (stateRef.current.isAnim) return;

      // Lock current width to prevent layout shifts during ASCII scrambling
      if (stateRef.current.origW === null) {
        stateRef.current.origW = el.getBoundingClientRect().width;
        el.style.width = `${stateRef.current.origW}px`;
      }

      stateRef.current.isAnim = true;
      el.classList.add("as");

      const animate = () => {
        const t = Date.now();

        // Evict finished waves
        stateRef.current.waves = stateRef.current.waves.filter(
          (w) => t - w.startTime < stateRef.current.dur
        );

        if (stateRef.current.waves.length === 0) {
          stop();
          return;
        }

        // Apply visual scramble
        el.textContent = genScrambledTxt(t);
        stateRef.current.animId = requestAnimationFrame(animate);
      };

      stateRef.current.animId = requestAnimationFrame(animate);
    };

    const startWave = () => {
      stateRef.current.waves.push({
        startPos: stateRef.current.cursorPos,
        startTime: Date.now(),
        id: Math.random(),
      });

      if (!stateRef.current.isAnim) start();
    };

    const calcWaveEffect = (charIdx: number, t: number) => {
      let shouldAnim = false;
      let resultChar = stateRef.current.origChars[charIdx];

      for (const w of stateRef.current.waves) {
        const age = t - w.startTime;
        const prog = Math.min(age / stateRef.current.dur, 1);
        const dist = Math.abs(charIdx - w.startPos);
        const maxDist = Math.max(w.startPos, stateRef.current.origChars.length - w.startPos - 1);
        const rad = (prog * (maxDist + WAVE_BUF)) / stateRef.current.spread;

        if (dist <= rad) {
          shouldAnim = true;
          const intens = Math.max(0, rad - dist);

          // Wave distortion characters
          if (intens <= WAVE_THRESH && intens > 0) {
            const index =
              (dist * CHAR_MULT + Math.floor(age / ANIM_STEP)) % stateRef.current.chars.length;
            resultChar = stateRef.current.chars[index];
          }
        }
      }

      return { shouldAnim, char: resultChar };
    };

    const genScrambledTxt = (t: number) =>
      stateRef.current.origChars
        .map((char, i) => {
          if (stateRef.current.preserveSpaces && char === " ") return " ";
          const res = calcWaveEffect(i, t);
          return res.shouldAnim ? res.char : char;
        })
        .join("");

    const handleEnter = (e: MouseEvent) => {
      stateRef.current.isHover = true;
      updateCursorPos(e);
      startWave();
    };

    const handleMove = (e: MouseEvent) => {
      if (!stateRef.current.isHover) return;
      const old = stateRef.current.cursorPos;
      updateCursorPos(e);
      if (stateRef.current.cursorPos !== old) startWave();
    };

    const handleLeave = () => {
      stateRef.current.isHover = false;
    };

    el.addEventListener("mouseenter", handleEnter);
    el.addEventListener("mousemove", handleMove);
    el.addEventListener("mouseleave", handleLeave);

    return () => {
      el.removeEventListener("mouseenter", handleEnter);
      el.removeEventListener("mousemove", handleMove);
      el.removeEventListener("mouseleave", handleLeave);
      if (stateRef.current.animId) {
        cancelAnimationFrame(stateRef.current.animId);
      }
    };
  }, [children]);

  return (
    <Component
      ref={elRef}
      className={cn(
        "cursor-pointer select-none relative inline-block transition-colors duration-200",
        className
      )}
      {...props}
    />
  );
}

export default AsciiGlitchRipple;

```

---

## `src\components\ui\aspect-ratio.tsx`

```tsx
"use client"

import * as AspectRatioPrimitive from "@radix-ui/react-aspect-ratio"

const AspectRatio = AspectRatioPrimitive.Root

export { AspectRatio }

```

---

## `src\components\ui\aurora-hero.tsx`

```tsx
"use client";

import React, { useState } from "react";
import { cn } from "@/lib/utils";

export interface AuroraHeroProps extends React.HTMLAttributes<HTMLDivElement> {
  /** The main title text to display with the glass displacement effect. */
  title?: string;
  /** Whether to show the toggle switch for background modes. */
  showSwitch?: boolean;
}

export function AuroraHero({
  title = "An awesome title",
  className,
  ...props
}: AuroraHeroProps) {

  // Safely URL-encoded SVG string for the fluted glass effect
  const filterImageHref = "data:image/svg+xml," + encodeURIComponent(`
    <svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 1 1' color-interpolation-filters='sRGB'>
      <g>
        <rect width='1' height='1' fill='black' />
        <rect width='1' height='1' fill='url(#red)' style='mix-blend-mode:screen' />
        <rect width='1' height='1' fill='url(#green)' style='mix-blend-mode:screen' />
        <rect width='1' height='1' fill='url(#yellow)' style='mix-blend-mode:screen' />
      </g>
      <defs>
        <radialGradient id='yellow' cx='0' cy='0' r='1' >
          <stop stop-color='yellow' />
          <stop stop-color='yellow' offset='1' stop-opacity='0' />
        </radialGradient>
        <radialGradient id='green' cx='1' cy='0' r='1' >
          <stop stop-color='green' />
          <stop stop-color='green' offset='1' stop-opacity='0' />
        </radialGradient>
        <radialGradient id='red' cx='0' cy='1' r='1' >
          <stop stop-color='red' />
          <stop stop-color='red' offset='1' stop-opacity='0' />
        </radialGradient>
      </defs>
    </svg>
  `);

  return (
    <section
      className={cn("aurora-hero-wrapper w-full min-h-[400px] h-[500px] sm:h-[600px] relative overflow-hidden", className)}
      {...props}
    >
      <style>{`
        .aurora-hero-wrapper {
          --stripe-color: #000;
          --bg-filter: blur(10px) opacity(50%) saturate(200%);
          background: var(--stripe-color);
          font-family: Inter, sans-serif;
        }
        :is(.dark) .aurora-hero-wrapper {
          --stripe-color: #fff;
          --bg-filter: blur(10px) invert(100%);
        }
        @keyframes smoothBg {
          from { background-position: 50% 50%, 50% 50%; }
          to { background-position: 350% 50%, 350% 50%; }
        }
        .aurora-hero-bg {
          width: 100%;
          height: 100%;
          position: absolute;
          inset: 0;
          --stripes: repeating-linear-gradient(
            100deg, 
            var(--stripe-color) 0%, 
            var(--stripe-color) 7%, 
            transparent 10%, 
            transparent 12%, 
            var(--stripe-color) 16%
          );
          --rainbow: repeating-linear-gradient(
            100deg, 
            #60a5fa 10%, 
            #e879f9 15%, 
            #60a5fa 20%, 
            #5eead4 25%, 
            #60a5fa 30%
          );
          background-image: var(--stripes), var(--rainbow);
          background-size: 300%, 200%;
          background-position: 50% 50%, 50% 50%;
          filter: var(--bg-filter);
          mask-image: radial-gradient(ellipse at 100% 0%, black 40%, transparent 70%);
          -webkit-mask-image: radial-gradient(ellipse at 100% 0%, black 40%, transparent 70%);
        }
        .aurora-hero-bg::after {
          content: "";
          position: absolute;
          inset: 0;
          background-image: var(--stripes), var(--rainbow);
          background-size: 200%, 100%;
          animation: smoothBg 60s linear infinite;
          background-attachment: fixed;
          mix-blend-mode: difference;
        }
        .aurora-content {
          position: absolute;
          inset: 0;
          width: 100%;
          height: 100%;
          display: flex;
          place-content: center;
          place-items: center;
          flex-flow: column;
          gap: 4.5%;
          text-align: center;
          backdrop-filter: contrast(0.9) blur(7px) url(#fluted);
          -webkit-backdrop-filter: contrast(0.9) blur(7px) url(#fluted);
          mix-blend-mode: difference;
          filter: invert(1);
        }
        .h1-scalingSize {
          font-size: calc(1rem - -5vw);
          position: relative;
          isolation: isolate;
          font-weight: 700;
        }
        .h1-scalingSize::first-letter {
          font-size: 300%;
        }
        .h1-scalingSize::before {
          content: attr(data-text);
          position: absolute;
          inset: 0;
          background: white;
          text-shadow: 0 0 1px #ffffff;
          background-clip: text;
          -webkit-text-fill-color: transparent;
          background-color: white;
          -webkit-mask: linear-gradient(#000 0 0) luminance;
          mask: linear-gradient(#000 0 0) luminance, alpha;
          backdrop-filter: blur(19px) brightness(12.5);
          -webkit-text-stroke: 1px white;
          display: flex;
          margin: auto;
          z-index: 1;
          pointer-events: none;
        }
      `}</style>

      <div className="aurora-hero-bg"></div>

      <div className="aurora-content">
        <h1 className="h1-scalingSize" data-text={title}>{title}</h1>
      </div>

      <svg
        version="1.1"
        xmlns="http://www.w3.org/2000/svg"
        xmlnsXlink="http://www.w3.org/1999/xlink"
        colorInterpolationFilters="sRGB"
        style={{ position: "absolute", opacity: 0, height: 0, width: 0, pointerEvents: "none" }}
        aria-hidden="true"
        focusable="false"
      >
        <filter id="fluted" primitiveUnits="objectBoundingBox">
          <feImage
            x="0"
            y="0"
            result="image_0"
            crossOrigin="anonymous"
            href={filterImageHref}
            preserveAspectRatio="none meet"
            width=".03"
            height="1"
          />
          <feTile in="image_0" result="tile_0" />
          <feGaussianBlur stdDeviation=".0001" edgeMode="none" in="tile_0" result="bar_smoothness" x="0" y="0" />
          <feDisplacementMap scale=".08" xChannelSelector="R" yChannelSelector="G" in="SourceGraphic" in2="bar_smoothness" result="displacement_0" />
        </filter>
      </svg>
    </section>
  );
}

export default AuroraHero;

```

---

## `src\components\ui\avatar.tsx`

```tsx
'use client'

import * as React from 'react'
import * as AvatarPrimitive from '@radix-ui/react-avatar'

import { cn } from '@/lib/utils'

function Avatar({ className, ...props }: React.ComponentProps<typeof AvatarPrimitive.Root>) {
    return (
        <AvatarPrimitive.Root
            data-slot="avatar"
            className={cn('relative flex size-8 shrink-0 overflow-hidden rounded-full', className)}
            {...props}
        />
    )
}

function AvatarImage({ className, ...props }: React.ComponentProps<typeof AvatarPrimitive.Image>) {
    return (
        <AvatarPrimitive.Image
            data-slot="avatar-image"
            className={cn('aspect-square size-full', className)}
            {...props}
        />
    )
}

function AvatarFallback({ className, ...props }: React.ComponentProps<typeof AvatarPrimitive.Fallback>) {
    return (
        <AvatarPrimitive.Fallback
            data-slot="avatar-fallback"
            className={cn('bg-muted flex size-full items-center justify-center rounded-full', className)}
            {...props}
        />
    )
}

export { Avatar, AvatarImage, AvatarFallback }

```

---

## `src\components\ui\awwwards-nav.tsx`

```tsx
"use client";

import * as React from "react";
import { useEffect, useRef, useState } from "react";
import gsap from "gsap";
import { List, X } from "@phosphor-icons/react";
import { cn } from "@/lib/utils";

/**
 * Awwwards Nav
 *
 * A glassmorphic bottom navigation pill with inline links and a "More" button.
 * Tapping "More" expands the bar upward with a smooth `power4.inOut` motion:
 * the inline links fade out, the button grows to full width, its icon flips to
 * an X, and a multi-column mega-menu reveals inside the expanded panel.
 *
 * Ported from the vanilla "CodeGrid Awwwards Nav" (GSAP) into a single,
 * self-contained, prop-driven React component. Positioning is left to the
 * consumer via `className`, so it works fixed to the viewport or absolutely
 * inside a positioned container.
 */

export interface AwwwardsNavLink {
  label: string;
  href: string;
}

export interface AwwwardsNavColumn {
  /** Column heading shown above its links. */
  title: string;
  /** Links listed under the heading. */
  links: AwwwardsNavLink[];
}

export interface AwwwardsNavProps {
  /** Inline links shown in the collapsed bar. Defaults to a sample set. */
  items?: AwwwardsNavLink[];
  /** Columns revealed in the expanded mega-menu. Defaults to a sample set. */
  columns?: AwwwardsNavColumn[];
  /** Label on the expand/collapse button. Defaults to "More". */
  moreLabel?: string;
  /** Called whenever the panel opens (true) or closes (false). */
  onOpenChange?: (open: boolean) => void;
  /**
   * Extra class names for the root nav. Use this to position it —
   * e.g. `fixed bottom-6 left-1/2 -translate-x-1/2` (default) or
   * `absolute` inside a positioned parent.
   */
  className?: string;
}

const DEFAULT_ITEMS: AwwwardsNavLink[] = [
  { label: "Home", href: "#" },
  { label: "Nominees", href: "#" },
  { label: "Directory", href: "#" },
  { label: "Collections", href: "#" },
];

const DEFAULT_COLUMNS: AwwwardsNavColumn[] = [
  {
    title: "Awards",
    links: [
      { label: "Winners", href: "#" },
      { label: "Site of the Day", href: "#" },
      { label: "Nominees", href: "#" },
    ],
  },
  {
    title: "Inspiration",
    links: [
      { label: "Collections", href: "#" },
      { label: "Elements", href: "#" },
      { label: "Resources", href: "#" },
    ],
  },
  {
    title: "Directory",
    links: [
      { label: "Professionals", href: "#" },
      { label: "Agencies", href: "#" },
      { label: "Freelancers", href: "#" },
    ],
  },
  {
    title: "Market",
    links: [
      { label: "Jobs", href: "#" },
      { label: "New Events", href: "#" },
      { label: "Products", href: "#" },
    ],
  },
];

const COLLAPSED_HEIGHT = 60;
const EXPANDED_HEIGHT = 370;

export function AwwwardsNav({
  items = DEFAULT_ITEMS,
  columns = DEFAULT_COLUMNS,
  moreLabel = "More",
  onOpenChange,
  className,
}: AwwwardsNavProps) {
  const navRef = useRef<HTMLElement>(null);
  const navTopRef = useRef<HTMLDivElement>(null);
  const navItemsRef = useRef<HTMLDivElement>(null);
  const navHomeRef = useRef<HTMLDivElement>(null);

  const openRef = useRef(false);
  const animatingRef = useRef(false);
  const [showClose, setShowClose] = useState(false);

  const onOpenChangeRef = useRef(onOpenChange);
  useEffect(() => {
    onOpenChangeRef.current = onOpenChange;
  }, [onOpenChange]);

  // Establish the collapsed baseline imperatively (matches the source's gsap.set).
  useEffect(() => {
    const nav = navRef.current;
    const navTop = navTopRef.current;
    const navItems = navItemsRef.current;
    const navHome = navHomeRef.current;
    if (!nav || !navTop || !navItems || !navHome) return;

    gsap.set(nav, { height: COLLAPSED_HEIGHT });
    gsap.set(navTop, { opacity: 0, scale: 0.9, display: "none" });
    gsap.set(navItems, { opacity: 1, display: "flex" });
    gsap.set(navHome, { flexGrow: 0 });

    return () => {
      gsap.killTweensOf([nav, navTop, navItems, navHome]);
    };
  }, []);

  const toggle = () => {
    const nav = navRef.current;
    const navTop = navTopRef.current;
    const navItems = navItemsRef.current;
    const navHome = navHomeRef.current;
    if (!nav || !navTop || !navItems || !navHome || animatingRef.current) return;

    animatingRef.current = true;
    const opening = !openRef.current;
    openRef.current = opening;
    onOpenChangeRef.current?.(opening);

    if (opening) {
      gsap.to(nav, { height: EXPANDED_HEIGHT, duration: 0.75, ease: "power4.inOut" });
      gsap.to(navItems, {
        opacity: 0,
        duration: 0.1,
        onComplete: () => gsap.set(navItems, { display: "none" }),
      });
      gsap.to(navHome, {
        flexGrow: 1,
        duration: 0.2,
        ease: "power4.inOut",
        onComplete: () => setShowClose(true),
      });
      gsap.to(navTop, {
        opacity: 1,
        scale: 1,
        duration: 0.3,
        delay: 0.5,
        onStart: () => gsap.set(navTop, { display: "block" }),
        onComplete: () => {
          animatingRef.current = false;
        },
      });
    } else {
      gsap.to(nav, { height: COLLAPSED_HEIGHT, duration: 0.75, ease: "power4.inOut", delay: 0.2 });
      gsap.to(navTop, {
        opacity: 0,
        scale: 0.9,
        duration: 0.2,
        onComplete: () => gsap.set(navTop, { display: "none" }),
      });
      gsap.to(navHome, {
        flexGrow: 0,
        duration: 0.2,
        ease: "power4.inOut",
        onComplete: () => setShowClose(false),
      });
      gsap.to(navItems, {
        opacity: 1,
        duration: 0.2,
        delay: 0.5,
        onStart: () => gsap.set(navItems, { display: "flex" }),
        onComplete: () => {
          animatingRef.current = false;
        },
      });
    }
  };

  return (
    <nav
      ref={navRef}
      className={cn(
        "fixed bottom-6 left-1/2 z-50 -translate-x-1/2",
        "h-[60px] w-[min(680px,92vw)] overflow-hidden rounded-xl border backdrop-blur-xl",
        "border-black/10 bg-white/70 dark:border-white/25 dark:bg-black/75",
        className,
      )}
    >
      {/* Expanded mega-menu, fills the space above the bottom row */}
      <div ref={navTopRef} className="absolute inset-x-0 top-0 bottom-[60px] hidden p-2.5">
        <div className="flex h-full w-full gap-0 rounded-[10px] border border-black/[0.06] bg-black/[0.03] p-5 dark:border-white/[0.06] dark:bg-white/[0.04]">
          {columns.map((col, ci) => (
            <div
              key={col.title}
              className={cn(
                "flex flex-1 flex-col gap-1",
                ci > 0 && "border-l border-dashed border-black/15 pl-4 dark:border-white/20",
              )}
            >
              <div className="mb-3 flex items-center gap-2">
                <span className="h-1 w-1 shrink-0 rounded-full bg-black dark:bg-white" />
                <p className="text-sm text-black/70 dark:text-white/75">{col.title}</p>
              </div>
              {col.links.map((link) => (
                <a
                  key={link.label}
                  href={link.href}
                  className="block py-2 text-sm text-black transition-colors hover:text-black/50 dark:text-white dark:hover:text-white/60"
                >
                  {link.label}
                </a>
              ))}
            </div>
          ))}
        </div>
      </div>

      {/* Collapsed bottom row: More button + inline items */}
      <div className="absolute inset-x-0 bottom-0 flex h-[60px] gap-1.5 p-2.5">
        <div
          ref={navHomeRef}
          role="button"
          tabIndex={0}
          aria-expanded={showClose}
          aria-label={showClose ? "Close menu" : "Open menu"}
          onClick={toggle}
          onKeyDown={(e) => {
            if (e.key === "Enter" || e.key === " ") {
              e.preventDefault();
              toggle();
            }
          }}
          className={cn(
            "flex shrink-0 cursor-pointer select-none items-center justify-center gap-2.5 rounded-[10px] border px-5 text-sm transition-colors",
            "border-black/10 bg-black/[0.04] text-neutral-600 hover:bg-black/[0.08] hover:text-black",
            "dark:border-white/10 dark:bg-white/[0.06] dark:text-neutral-300 dark:hover:bg-white/[0.12] dark:hover:text-white",
            showClose && "bg-black/[0.08] text-black dark:bg-white/[0.12] dark:text-white",
          )}
        >
          {showClose ? (
            <X weight="light" className="h-4 w-4 text-black dark:text-white" />
          ) : (
            <List weight="light" className="h-4 w-4 text-black dark:text-white" />
          )}
          <span>{moreLabel}</span>
        </div>

        <div ref={navItemsRef} className="flex min-w-0 flex-[4] items-center gap-1.5">
          {items.map((item) => (
            <div
              key={item.label}
              className="flex h-full flex-1 items-center justify-center rounded-[10px] border border-black/15 transition-colors hover:border-black/40 dark:border-white/20 dark:hover:border-white/50"
            >
              <a
                href={item.href}
                className="px-2 text-center text-sm text-neutral-600 transition-colors hover:text-black dark:text-neutral-400 dark:hover:text-white"
              >
                {item.label}
              </a>
            </div>
          ))}
        </div>
      </div>
    </nav>
  );
}

export default AwwwardsNav;

```

---

## `src\components\ui\badge.tsx`

```tsx
import * as React from "react"
import { Slot } from "@radix-ui/react-slot"
import { cva, type VariantProps } from "class-variance-authority"

import { cn } from "@/lib/utils"

const badgeVariants = cva(
  "inline-flex items-center justify-center rounded-full border px-2 py-0.5 text-xs font-medium w-fit whitespace-nowrap shrink-0 [&>svg]:size-3 gap-1 [&>svg]:pointer-events-none focus-visible:border-ring focus-visible:ring-ring/50 focus-visible:ring-[3px] aria-invalid:ring-destructive/20 dark:aria-invalid:ring-destructive/40 aria-invalid:border-destructive transition-[color,box-shadow] overflow-hidden",
  {
    variants: {
      variant: {
        default:
          "border-transparent bg-primary text-primary-foreground [a&]:hover:bg-primary/90",
        secondary:
          "border-transparent bg-secondary text-secondary-foreground [a&]:hover:bg-secondary/90",
        destructive:
          "border-transparent bg-destructive text-white [a&]:hover:bg-destructive/90 focus-visible:ring-destructive/20 dark:focus-visible:ring-destructive/40 dark:bg-destructive/60",
        outline:
          "text-foreground [a&]:hover:bg-accent [a&]:hover:text-accent-foreground",
      },
    },
    defaultVariants: {
      variant: "default",
    },
  }
)

function Badge({
  className,
  variant,
  asChild = false,
  ...props
}: React.ComponentProps<"span"> &
  VariantProps<typeof badgeVariants> & { asChild?: boolean }) {
  const Comp = asChild ? Slot : "span"

  return (
    <Comp
      data-slot="badge"
      className={cn(badgeVariants({ variant }), className)}
      {...props}
    />
  )
}

export { Badge, badgeVariants }

```

---

## `src\components\ui\border-beam.tsx`

```tsx
"use client";

import { cn } from "@/lib/utils";

interface BorderBeamProps {
    className?: string;
    size?: number;
    duration?: number;
    borderWidth?: number;
    anchor?: number;
    colorFrom?: string;
    colorTo?: string;
    delay?: number;
}

export const BorderBeam = ({
    className,
    size = 200,
    duration = 15,
    anchor = 90,
    borderWidth = 1.5,
    colorFrom = "#ffaa40",
    colorTo = "#9c40ff",
    delay = 0,
}: BorderBeamProps) => {
    return (
        <>
            <style jsx>{`
        @keyframes border-beam {
          100% {
            offset-distance: 100%;
          }
        }
      `}</style>
            <div
                style={
                    {
                        "--size": size,
                        "--duration": duration,
                        "--anchor": anchor,
                        "--border-width": borderWidth,
                        "--color-from": colorFrom,
                        "--color-to": colorTo,
                        "--delay": delay,
                    } as React.CSSProperties
                }
                className={cn(
                    "absolute inset-[0] rounded-[inherit] [border:calc(var(--border-width)*1px)_solid_transparent]",
                    "![mask-clip:padding-box,border-box] ![mask-composite:intersect] [mask:linear-gradient(transparent,transparent),linear-gradient(white,white)]",
                    "after:absolute after:aspect-square after:w-[calc(var(--size)*1px)] after:[animation:border-beam_calc(var(--duration)*1s)_infinite_linear] after:[animation-delay:var(--delay)s] after:[background:linear-gradient(to_left,var(--color-from),var(--color-to),transparent)] after:[offset-anchor:calc(var(--anchor)*1%)_50%] after:[offset-path:rect(0_auto_auto_0_round_calc(var(--size)*1px))]",
                    className,
                )}
            />
        </>
    );
};

```

---

## `src\components\ui\breadcrumb.tsx`

```tsx
import * as React from "react"
import { Slot } from "@radix-ui/react-slot"
import { ChevronRight, MoreHorizontal } from "lucide-react"

import { cn } from "@/lib/utils"

function Breadcrumb({ ...props }: React.ComponentProps<"nav">) {
  return <nav aria-label="breadcrumb" data-slot="breadcrumb" {...props} />
}

function BreadcrumbList({ className, ...props }: React.ComponentProps<"ol">) {
  return (
    <ol
      data-slot="breadcrumb-list"
      className={cn(
        "text-muted-foreground flex flex-wrap items-center gap-1.5 text-sm break-words sm:gap-2.5",
        className
      )}
      {...props}
    />
  )
}

function BreadcrumbItem({ className, ...props }: React.ComponentProps<"li">) {
  return (
    <li
      data-slot="breadcrumb-item"
      className={cn("inline-flex items-center gap-1.5", className)}
      {...props}
    />
  )
}

function BreadcrumbLink({
  asChild,
  className,
  ...props
}: React.ComponentProps<"a"> & {
  asChild?: boolean
}) {
  const Comp = asChild ? Slot : "a"

  return (
    <Comp
      data-slot="breadcrumb-link"
      className={cn("hover:text-foreground transition-colors", className)}
      {...props}
    />
  )
}

function BreadcrumbPage({ className, ...props }: React.ComponentProps<"span">) {
  return (
    <span
      data-slot="breadcrumb-page"
      role="link"
      aria-disabled="true"
      aria-current="page"
      className={cn("text-foreground font-normal", className)}
      {...props}
    />
  )
}

function BreadcrumbSeparator({
  children,
  className,
  ...props
}: React.ComponentProps<"li">) {
  return (
    <li
      data-slot="breadcrumb-separator"
      role="presentation"
      aria-hidden="true"
      className={cn("[&>svg]:size-3.5", className)}
      {...props}
    >
      {children ?? <ChevronRight />}
    </li>
  )
}

function BreadcrumbEllipsis({
  className,
  ...props
}: React.ComponentProps<"span">) {
  return (
    <span
      data-slot="breadcrumb-ellipsis"
      role="presentation"
      aria-hidden="true"
      className={cn("flex size-9 items-center justify-center", className)}
      {...props}
    >
      <MoreHorizontal className="size-4" />
      <span className="sr-only">More</span>
    </span>
  )
}

export {
  Breadcrumb,
  BreadcrumbList,
  BreadcrumbItem,
  BreadcrumbLink,
  BreadcrumbPage,
  BreadcrumbSeparator,
  BreadcrumbEllipsis,
}

```

---

## `src\components\ui\button-group.tsx`

```tsx
import { Slot } from "@radix-ui/react-slot"
import { cva, type VariantProps } from "class-variance-authority"

import { cn } from "@/lib/utils"
import { Separator } from "@/components/ui/separator"

const buttonGroupVariants = cva(
  "flex w-fit items-stretch [&>*]:focus-visible:z-10 [&>*]:focus-visible:relative [&>[data-slot=select-trigger]:not([class*='w-'])]:w-fit [&>input]:flex-1 has-[select[aria-hidden=true]:last-child]:[&>[data-slot=select-trigger]:last-of-type]:rounded-r-md has-[>[data-slot=button-group]]:gap-2",
  {
    variants: {
      orientation: {
        horizontal:
          "[&>*:not(:first-child)]:rounded-l-none [&>*:not(:first-child)]:border-l-0 [&>*:not(:last-child)]:rounded-r-none",
        vertical:
          "flex-col [&>*:not(:first-child)]:rounded-t-none [&>*:not(:first-child)]:border-t-0 [&>*:not(:last-child)]:rounded-b-none",
      },
    },
    defaultVariants: {
      orientation: "horizontal",
    },
  }
)

function ButtonGroup({
  className,
  orientation,
  ...props
}: React.ComponentProps<"div"> & VariantProps<typeof buttonGroupVariants>) {
  return (
    <div
      role="group"
      data-slot="button-group"
      data-orientation={orientation}
      className={cn(buttonGroupVariants({ orientation }), className)}
      {...props}
    />
  )
}

function ButtonGroupText({
  className,
  asChild = false,
  ...props
}: React.ComponentProps<"div"> & {
  asChild?: boolean
}) {
  const Comp = asChild ? Slot : "div"

  return (
    <Comp
      className={cn(
        "bg-muted flex items-center gap-2 rounded-md border px-4 text-sm font-medium shadow-xs [&_svg]:pointer-events-none [&_svg:not([class*='size-'])]:size-4",
        className
      )}
      {...props}
    />
  )
}

function ButtonGroupSeparator({
  className,
  orientation = "vertical",
  ...props
}: React.ComponentProps<typeof Separator>) {
  return (
    <Separator
      data-slot="button-group-separator"
      orientation={orientation}
      className={cn(
        "bg-input relative !m-0 self-stretch data-[orientation=vertical]:h-auto",
        className
      )}
      {...props}
    />
  )
}

export {
  ButtonGroup,
  ButtonGroupSeparator,
  ButtonGroupText,
  buttonGroupVariants,
}

```

---

## `src\components\ui\button.tsx`

```tsx
import * as React from 'react'
import { Slot } from '@radix-ui/react-slot'
import { cva, type VariantProps } from 'class-variance-authority'

import { cn } from '@/lib/utils'

const buttonVariants = cva('cursor-pointer inline-flex items-center justify-center gap-2 whitespace-nowrap rounded-md text-sm font-medium transition-colors focus-visible:outline-none focus-visible:ring-1 focus-visible:ring-ring disabled:pointer-events-none disabled:opacity-50 [&_svg]:pointer-events-none [&_svg]:size-4 [&_svg]:shrink-0', {
    variants: {
        variant: {
            default: 'shadow-sm shadow-black/20 bg-primary text-primary-foreground hover:bg-primary/90',
            destructive: 'bg-destructive text-destructive-foreground shadow-md hover:bg-destructive/90',
            outline: 'shadow-sm shadow-black/15 border border-transparent bg-background ring-1 ring-foreground/10 duration-200 hover:bg-muted/50 dark:ring-foreground/15 dark:hover:bg-muted/50',
            secondary: 'bg-secondary text-secondary-foreground shadow-sm hover:bg-secondary/80',
            ghost: 'hover:bg-accent hover:text-accent-foreground',
            link: 'text-primary underline-offset-4 hover:underline',
        },
        size: {
            default: 'h-9 px-4 py-2',
            sm: 'h-8 rounded-md px-3 text-xs',
            lg: 'h-10 rounded-md px-8',
            icon: 'h-9 w-9',
        },
    },
    defaultVariants: {
        variant: 'default',
        size: 'default',
    },
})

export interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement>, VariantProps<typeof buttonVariants> {
    asChild?: boolean
}

const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(({ className, variant, size, asChild = false, ...props }, ref) => {
    const Comp = asChild ? Slot : 'button'
    return (
        <Comp
            className={cn(buttonVariants({ variant, size, className }))}
            ref={ref}
            {...props}
        />
    )
})
Button.displayName = 'Button'

export { Button, buttonVariants }

```

---

## `src\components\ui\calendar.tsx`

```tsx
"use client"

import * as React from "react"
import {
  ChevronDownIcon,
  ChevronLeftIcon,
  ChevronRightIcon,
} from "lucide-react"
import { DayButton, DayPicker, getDefaultClassNames } from "react-day-picker"

import { cn } from "@/lib/utils"
import { Button, buttonVariants } from "@/components/ui/button"

function Calendar({
  className,
  classNames,
  showOutsideDays = true,
  captionLayout = "label",
  buttonVariant = "ghost",
  formatters,
  components,
  ...props
}: React.ComponentProps<typeof DayPicker> & {
  buttonVariant?: React.ComponentProps<typeof Button>["variant"]
}) {
  const defaultClassNames = getDefaultClassNames()

  return (
    <DayPicker
      showOutsideDays={showOutsideDays}
      className={cn(
        "bg-background group/calendar p-3 [--cell-size:--spacing(8)] [[data-slot=card-content]_&]:bg-transparent [[data-slot=popover-content]_&]:bg-transparent",
        String.raw`rtl:**:[.rdp-button\_next>svg]:rotate-180`,
        String.raw`rtl:**:[.rdp-button\_previous>svg]:rotate-180`,
        className
      )}
      captionLayout={captionLayout}
      formatters={{
        formatMonthDropdown: (date) =>
          date.toLocaleString("default", { month: "short" }),
        ...formatters,
      }}
      classNames={{
        root: cn("w-fit", defaultClassNames.root),
        months: cn(
          "flex gap-4 flex-col md:flex-row relative",
          defaultClassNames.months
        ),
        month: cn("flex flex-col w-full gap-4", defaultClassNames.month),
        nav: cn(
          "flex items-center gap-1 w-full absolute top-0 inset-x-0 justify-between",
          defaultClassNames.nav
        ),
        button_previous: cn(
          buttonVariants({ variant: buttonVariant }),
          "size-(--cell-size) aria-disabled:opacity-50 p-0 select-none",
          defaultClassNames.button_previous
        ),
        button_next: cn(
          buttonVariants({ variant: buttonVariant }),
          "size-(--cell-size) aria-disabled:opacity-50 p-0 select-none",
          defaultClassNames.button_next
        ),
        month_caption: cn(
          "flex items-center justify-center h-(--cell-size) w-full px-(--cell-size)",
          defaultClassNames.month_caption
        ),
        dropdowns: cn(
          "w-full flex items-center text-sm font-medium justify-center h-(--cell-size) gap-1.5",
          defaultClassNames.dropdowns
        ),
        dropdown_root: cn(
          "relative has-focus:border-ring border border-input shadow-xs has-focus:ring-ring/50 has-focus:ring-[3px] rounded-md",
          defaultClassNames.dropdown_root
        ),
        dropdown: cn(
          "absolute bg-popover inset-0 opacity-0",
          defaultClassNames.dropdown
        ),
        caption_label: cn(
          "select-none font-medium",
          captionLayout === "label"
            ? "text-sm"
            : "rounded-md pl-2 pr-1 flex items-center gap-1 text-sm h-8 [&>svg]:text-muted-foreground [&>svg]:size-3.5",
          defaultClassNames.caption_label
        ),
        table: "w-full border-collapse",
        weekdays: cn("flex", defaultClassNames.weekdays),
        weekday: cn(
          "text-muted-foreground rounded-md flex-1 font-normal text-[0.8rem] select-none",
          defaultClassNames.weekday
        ),
        week: cn("flex w-full mt-2", defaultClassNames.week),
        week_number_header: cn(
          "select-none w-(--cell-size)",
          defaultClassNames.week_number_header
        ),
        week_number: cn(
          "text-[0.8rem] select-none text-muted-foreground",
          defaultClassNames.week_number
        ),
        day: cn(
          "relative w-full h-full p-0 text-center [&:last-child[data-selected=true]_button]:rounded-r-md group/day aspect-square select-none",
          props.showWeekNumber
            ? "[&:nth-child(2)[data-selected=true]_button]:rounded-l-md"
            : "[&:first-child[data-selected=true]_button]:rounded-l-md",
          defaultClassNames.day
        ),
        range_start: cn(
          "rounded-l-md bg-accent",
          defaultClassNames.range_start
        ),
        range_middle: cn("rounded-none", defaultClassNames.range_middle),
        range_end: cn("rounded-r-md bg-accent", defaultClassNames.range_end),
        today: cn(
          "bg-accent text-accent-foreground rounded-md data-[selected=true]:rounded-none",
          defaultClassNames.today
        ),
        outside: cn(
          "text-muted-foreground aria-selected:text-muted-foreground",
          defaultClassNames.outside
        ),
        disabled: cn(
          "text-muted-foreground opacity-50",
          defaultClassNames.disabled
        ),
        hidden: cn("invisible", defaultClassNames.hidden),
        ...classNames,
      }}
      components={{
        Root: ({ className, rootRef, ...props }) => {
          return (
            <div
              data-slot="calendar"
              ref={rootRef}
              className={cn(className)}
              {...props}
            />
          )
        },
        Chevron: ({ className, orientation, ...props }) => {
          if (orientation === "left") {
            return (
              <ChevronLeftIcon className={cn("size-4", className)} {...props} />
            )
          }

          if (orientation === "right") {
            return (
              <ChevronRightIcon
                className={cn("size-4", className)}
                {...props}
              />
            )
          }

          return (
            <ChevronDownIcon className={cn("size-4", className)} {...props} />
          )
        },
        DayButton: CalendarDayButton,
        WeekNumber: ({ children, ...props }) => {
          return (
            <td {...props}>
              <div className="flex size-(--cell-size) items-center justify-center text-center">
                {children}
              </div>
            </td>
          )
        },
        ...components,
      }}
      {...props}
    />
  )
}

function CalendarDayButton({
  className,
  day,
  modifiers,
  ...props
}: React.ComponentProps<typeof DayButton>) {
  const defaultClassNames = getDefaultClassNames()

  const ref = React.useRef<HTMLButtonElement>(null)
  React.useEffect(() => {
    if (modifiers.focused) ref.current?.focus()
  }, [modifiers.focused])

  return (
    <Button
      ref={ref}
      variant="ghost"
      size="icon"
      data-day={day.date.toLocaleDateString()}
      data-selected-single={
        modifiers.selected &&
        !modifiers.range_start &&
        !modifiers.range_end &&
        !modifiers.range_middle
      }
      data-range-start={modifiers.range_start}
      data-range-end={modifiers.range_end}
      data-range-middle={modifiers.range_middle}
      className={cn(
        "data-[selected-single=true]:bg-primary data-[selected-single=true]:text-primary-foreground data-[range-middle=true]:bg-accent data-[range-middle=true]:text-accent-foreground data-[range-start=true]:bg-primary data-[range-start=true]:text-primary-foreground data-[range-end=true]:bg-primary data-[range-end=true]:text-primary-foreground group-data-[focused=true]/day:border-ring group-data-[focused=true]/day:ring-ring/50 dark:hover:text-accent-foreground flex aspect-square size-auto w-full min-w-(--cell-size) flex-col gap-1 leading-none font-normal group-data-[focused=true]/day:relative group-data-[focused=true]/day:z-10 group-data-[focused=true]/day:ring-[3px] data-[range-end=true]:rounded-md data-[range-end=true]:rounded-r-md data-[range-middle=true]:rounded-none data-[range-start=true]:rounded-md data-[range-start=true]:rounded-l-md [&>span]:text-xs [&>span]:opacity-70",
        defaultClassNames.day,
        className
      )}
      {...props}
    />
  )
}

export { Calendar, CalendarDayButton }

```

---

## `src\components\ui\candy-button.tsx`

```tsx
import React from "react";
import { cn } from "@/lib/utils";

export interface CandyButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {}

export function CandyButton({ className, children = "Candy Button", ...props }: CandyButtonProps) {
  return (
    <button
      className={cn(
        "relative text-white font-semibold text-base leading-[22px] tracking-[0.02em]",
        "px-9 py-3 rounded-xl cursor-pointer transition-all duration-200 ease-out",
        "border border-[#54A1FD] bg-[radial-gradient(95%_60%_at_50%_75%,#005FD6_0%,#209BFF_100%)]",
        "shadow-[0px_4px_48px_-12px_#1187FF,inset_0px_1px_8px_-4px_#FFFFFF]",
        "active:scale-95 active:rotate-1",
        "after:absolute after:top-[1px] after:right-[10%] after:w-[60%] after:h-[1px]",
        "after:bg-gradient-to-r after:from-transparent after:via-white/50 after:to-transparent",
        "hover:brightness-110",
        className
      )}
      {...props}
    >
      {children}
    </button>
  );
}

export default CandyButton;

```

---

## `src\components\ui\card.tsx`

```tsx
import * as React from 'react'

import { cn } from '@/lib/utils'

function Card({ className, ...props }: React.ComponentProps<'div'>) {
    return (
        <div
            data-slot="card"
            className={cn('bg-card text-card-foreground rounded-xl border shadow-sm', className)}
            {...props}
        />
    )
}

function CardHeader({ className, ...props }: React.ComponentProps<'div'>) {
    return (
        <div
            data-slot="card-header"
            className={cn('flex flex-col gap-1.5 p-6', className)}
            {...props}
        />
    )
}

function CardTitle({ className, ...props }: React.ComponentProps<'div'>) {
    return (
        <div
            data-slot="card-title"
            className={cn('font-semibold leading-none tracking-tight', className)}
            {...props}
        />
    )
}

function CardDescription({ className, ...props }: React.ComponentProps<'div'>) {
    return (
        <div
            data-slot="card-description"
            className={cn('text-muted-foreground text-sm', className)}
            {...props}
        />
    )
}

function CardContent({ className, ...props }: React.ComponentProps<'div'>) {
    return (
        <div
            data-slot="card-content"
            className={cn('p-6 pt-0', className)}
            {...props}
        />
    )
}

function CardFooter({ className, ...props }: React.ComponentProps<'div'>) {
    return (
        <div
            data-slot="card-footer"
            className={cn('flex items-center p-6 pt-0', className)}
            {...props}
        />
    )
}

export { Card, CardHeader, CardFooter, CardTitle, CardDescription, CardContent }

```

---

## `src\components\ui\carousel.tsx`

```tsx
"use client"

import * as React from "react"
import useEmblaCarousel, {
  type UseEmblaCarouselType,
} from "embla-carousel-react"
import { ArrowLeft, ArrowRight } from "lucide-react"

import { cn } from "@/lib/utils"
import { Button } from "@/components/ui/button"

type CarouselApi = UseEmblaCarouselType[1]
type UseCarouselParameters = Parameters<typeof useEmblaCarousel>
type CarouselOptions = UseCarouselParameters[0]
type CarouselPlugin = UseCarouselParameters[1]

type CarouselProps = {
  opts?: CarouselOptions
  plugins?: CarouselPlugin
  orientation?: "horizontal" | "vertical"
  setApi?: (api: CarouselApi) => void
}

type CarouselContextProps = {
  carouselRef: ReturnType<typeof useEmblaCarousel>[0]
  api: ReturnType<typeof useEmblaCarousel>[1]
  scrollPrev: () => void
  scrollNext: () => void
  canScrollPrev: boolean
  canScrollNext: boolean
} & CarouselProps

const CarouselContext = React.createContext<CarouselContextProps | null>(null)

function useCarousel() {
  const context = React.useContext(CarouselContext)

  if (!context) {
    throw new Error("useCarousel must be used within a <Carousel />")
  }

  return context
}

function Carousel({
  orientation = "horizontal",
  opts,
  setApi,
  plugins,
  className,
  children,
  ...props
}: React.ComponentProps<"div"> & CarouselProps) {
  const [carouselRef, api] = useEmblaCarousel(
    {
      ...opts,
      axis: orientation === "horizontal" ? "x" : "y",
    },
    plugins
  )
  const [canScrollPrev, setCanScrollPrev] = React.useState(false)
  const [canScrollNext, setCanScrollNext] = React.useState(false)

  const onSelect = React.useCallback((api: CarouselApi) => {
    if (!api) return
    setCanScrollPrev(api.canScrollPrev())
    setCanScrollNext(api.canScrollNext())
  }, [])

  const scrollPrev = React.useCallback(() => {
    api?.scrollPrev()
  }, [api])

  const scrollNext = React.useCallback(() => {
    api?.scrollNext()
  }, [api])

  const handleKeyDown = React.useCallback(
    (event: React.KeyboardEvent<HTMLDivElement>) => {
      if (event.key === "ArrowLeft") {
        event.preventDefault()
        scrollPrev()
      } else if (event.key === "ArrowRight") {
        event.preventDefault()
        scrollNext()
      }
    },
    [scrollPrev, scrollNext]
  )

  React.useEffect(() => {
    if (!api || !setApi) return
    setApi(api)
  }, [api, setApi])

  React.useEffect(() => {
    if (!api) return
    onSelect(api)
    api.on("reInit", onSelect)
    api.on("select", onSelect)

    return () => {
      api?.off("select", onSelect)
    }
  }, [api, onSelect])

  return (
    <CarouselContext.Provider
      value={{
        carouselRef,
        api: api,
        opts,
        orientation:
          orientation || (opts?.axis === "y" ? "vertical" : "horizontal"),
        scrollPrev,
        scrollNext,
        canScrollPrev,
        canScrollNext,
      }}
    >
      <div
        onKeyDownCapture={handleKeyDown}
        className={cn("relative", className)}
        role="region"
        aria-roledescription="carousel"
        data-slot="carousel"
        {...props}
      >
        {children}
      </div>
    </CarouselContext.Provider>
  )
}

function CarouselContent({ className, ...props }: React.ComponentProps<"div">) {
  const { carouselRef, orientation } = useCarousel()

  return (
    <div
      ref={carouselRef}
      className="overflow-hidden"
      data-slot="carousel-content"
    >
      <div
        className={cn(
          "flex",
          orientation === "horizontal" ? "-ml-4" : "-mt-4 flex-col",
          className
        )}
        {...props}
      />
    </div>
  )
}

function CarouselItem({ className, ...props }: React.ComponentProps<"div">) {
  const { orientation } = useCarousel()

  return (
    <div
      role="group"
      aria-roledescription="slide"
      data-slot="carousel-item"
      className={cn(
        "min-w-0 shrink-0 grow-0 basis-full",
        orientation === "horizontal" ? "pl-4" : "pt-4",
        className
      )}
      {...props}
    />
  )
}

function CarouselPrevious({
  className,
  variant = "outline",
  size = "icon",
  ...props
}: React.ComponentProps<typeof Button>) {
  const { orientation, scrollPrev, canScrollPrev } = useCarousel()

  return (
    <Button
      data-slot="carousel-previous"
      variant={variant}
      size={size}
      className={cn(
        "absolute size-8 rounded-full",
        orientation === "horizontal"
          ? "top-1/2 -left-12 -translate-y-1/2"
          : "-top-12 left-1/2 -translate-x-1/2 rotate-90",
        className
      )}
      disabled={!canScrollPrev}
      onClick={scrollPrev}
      {...props}
    >
      <ArrowLeft />
      <span className="sr-only">Previous slide</span>
    </Button>
  )
}

function CarouselNext({
  className,
  variant = "outline",
  size = "icon",
  ...props
}: React.ComponentProps<typeof Button>) {
  const { orientation, scrollNext, canScrollNext } = useCarousel()

  return (
    <Button
      data-slot="carousel-next"
      variant={variant}
      size={size}
      className={cn(
        "absolute size-8 rounded-full",
        orientation === "horizontal"
          ? "top-1/2 -right-12 -translate-y-1/2"
          : "-bottom-12 left-1/2 -translate-x-1/2 rotate-90",
        className
      )}
      disabled={!canScrollNext}
      onClick={scrollNext}
      {...props}
    >
      <ArrowRight />
      <span className="sr-only">Next slide</span>
    </Button>
  )
}

export {
  type CarouselApi,
  Carousel,
  CarouselContent,
  CarouselItem,
  CarouselPrevious,
  CarouselNext,
}

```

---

## `src\components\ui\chart.tsx`

```tsx
"use client"

import * as React from "react"
import * as RechartsPrimitive from "recharts"

import { cn } from "@/lib/utils"

// Format: { THEME_NAME: CSS_SELECTOR }
const THEMES = { light: "", dark: ".dark" } as const

export type ChartConfig = {
  [k in string]: {
    label?: React.ReactNode
    icon?: React.ComponentType
  } & (
    | { color?: string; theme?: never }
    | { color?: never; theme: Record<keyof typeof THEMES, string> }
  )
}

type ChartContextProps = {
  config: ChartConfig
}

const ChartContext = React.createContext<ChartContextProps | null>(null)

function useChart() {
  const context = React.useContext(ChartContext)

  if (!context) {
    throw new Error("useChart must be used within a <ChartContainer />")
  }

  return context
}

function ChartContainer({
  id,
  className,
  children,
  config,
  ...props
}: React.ComponentProps<"div"> & {
  config: ChartConfig
  children: React.ComponentProps<
    typeof RechartsPrimitive.ResponsiveContainer
  >["children"]
}) {
  const uniqueId = React.useId()
  const chartId = `chart-${id || uniqueId.replace(/:/g, "")}`

  return (
    <ChartContext.Provider value={{ config }}>
      <div
        data-slot="chart"
        data-chart={chartId}
        className={cn(
          "[&_.recharts-cartesian-axis-tick_text]:fill-muted-foreground [&_.recharts-cartesian-grid_line[stroke='#ccc']]:stroke-border/50 [&_.recharts-curve.recharts-tooltip-cursor]:stroke-border [&_.recharts-polar-grid_[stroke='#ccc']]:stroke-border [&_.recharts-radial-bar-background-sector]:fill-muted [&_.recharts-rectangle.recharts-tooltip-cursor]:fill-muted [&_.recharts-reference-line_[stroke='#ccc']]:stroke-border flex aspect-video justify-center text-xs [&_.recharts-dot[stroke='#fff']]:stroke-transparent [&_.recharts-layer]:outline-hidden [&_.recharts-sector]:outline-hidden [&_.recharts-sector[stroke='#fff']]:stroke-transparent [&_.recharts-surface]:outline-hidden",
          className
        )}
        {...props}
      >
        <ChartStyle id={chartId} config={config} />
        <RechartsPrimitive.ResponsiveContainer>
          {children}
        </RechartsPrimitive.ResponsiveContainer>
      </div>
    </ChartContext.Provider>
  )
}

const ChartStyle = ({ id, config }: { id: string; config: ChartConfig }) => {
  const colorConfig = Object.entries(config).filter(
    ([, config]) => config.theme || config.color
  )

  if (!colorConfig.length) {
    return null
  }

  return (
    <style
      dangerouslySetInnerHTML={{
        __html: Object.entries(THEMES)
          .map(
            ([theme, prefix]) => `
${prefix} [data-chart=${id}] {
${colorConfig
  .map(([key, itemConfig]) => {
    const color =
      itemConfig.theme?.[theme as keyof typeof itemConfig.theme] ||
      itemConfig.color
    return color ? `  --color-${key}: ${color};` : null
  })
  .join("\n")}
}
`
          )
          .join("\n"),
      }}
    />
  )
}

const ChartTooltip = RechartsPrimitive.Tooltip

type ChartTooltipPayloadItem = {
  dataKey?: string | number
  name?: string
  value?: number | string
  color?: string
  type?: string
  payload?: Record<string, unknown> & { fill?: string }
}

interface ChartTooltipContentProps extends React.ComponentProps<"div"> {
  active?: boolean
  payload?: ChartTooltipPayloadItem[]
  indicator?: "line" | "dot" | "dashed"
  hideLabel?: boolean
  hideIndicator?: boolean
  label?: React.ReactNode
  labelFormatter?: (label: React.ReactNode, payload: ChartTooltipPayloadItem[]) => React.ReactNode
  labelClassName?: string
  formatter?: (
    value: number | string,
    name: string,
    item: ChartTooltipPayloadItem,
    index: number,
    payload: ChartTooltipPayloadItem["payload"]
  ) => React.ReactNode
  color?: string
  nameKey?: string
  labelKey?: string
}

function ChartTooltipContent({
  active,
  payload,
  className,
  indicator = "dot",
  hideLabel = false,
  hideIndicator = false,
  label,
  labelFormatter,
  labelClassName,
  formatter,
  color,
  nameKey,
  labelKey,
}: ChartTooltipContentProps) {
  const { config } = useChart()

  const tooltipLabel = React.useMemo(() => {
    if (hideLabel || !payload?.length) {
      return null
    }

    const [item] = payload
    const key = `${labelKey || item?.dataKey || item?.name || "value"}`
    const itemConfig = getPayloadConfigFromPayload(config, item, key)
    const value =
      !labelKey && typeof label === "string"
        ? config[label as keyof typeof config]?.label || label
        : itemConfig?.label

    if (labelFormatter) {
      return (
        <div className={cn("font-medium", labelClassName)}>
          {labelFormatter(value, payload)}
        </div>
      )
    }

    if (!value) {
      return null
    }

    return <div className={cn("font-medium", labelClassName)}>{value}</div>
  }, [
    label,
    labelFormatter,
    payload,
    hideLabel,
    labelClassName,
    config,
    labelKey,
  ])

  if (!active || !payload?.length) {
    return null
  }

  const nestLabel = payload.length === 1 && indicator !== "dot"

  return (
    <div
      className={cn(
        "border-border/50 bg-background grid min-w-[8rem] items-start gap-1.5 rounded-lg border px-2.5 py-1.5 text-xs shadow-xl",
        className
      )}
    >
      {!nestLabel ? tooltipLabel : null}
      <div className="grid gap-1.5">
        {payload
          .filter((item) => item.type !== "none")
          .map((item, index) => {
            const key = `${nameKey || item.name || item.dataKey || "value"}`
            const itemConfig = getPayloadConfigFromPayload(config, item, key)
            const indicatorColor = color || item.payload?.fill || item.color

            return (
              <div
                key={item.dataKey}
                className={cn(
                  "[&>svg]:text-muted-foreground flex w-full flex-wrap items-stretch gap-2 [&>svg]:h-2.5 [&>svg]:w-2.5",
                  indicator === "dot" && "items-center"
                )}
              >
                {formatter && item?.value !== undefined && item.name ? (
                  formatter(item.value, item.name, item, index, item.payload)
                ) : (
                  <>
                    {itemConfig?.icon ? (
                      <itemConfig.icon />
                    ) : (
                      !hideIndicator && (
                        <div
                          className={cn(
                            "shrink-0 rounded-[2px] border-(--color-border) bg-(--color-bg)",
                            {
                              "h-2.5 w-2.5": indicator === "dot",
                              "w-1": indicator === "line",
                              "w-0 border-[1.5px] border-dashed bg-transparent":
                                indicator === "dashed",
                              "my-0.5": nestLabel && indicator === "dashed",
                            }
                          )}
                          style={
                            {
                              "--color-bg": indicatorColor,
                              "--color-border": indicatorColor,
                            } as React.CSSProperties
                          }
                        />
                      )
                    )}
                    <div
                      className={cn(
                        "flex flex-1 justify-between leading-none",
                        nestLabel ? "items-end" : "items-center"
                      )}
                    >
                      <div className="grid gap-1.5">
                        {nestLabel ? tooltipLabel : null}
                        <span className="text-muted-foreground">
                          {itemConfig?.label || item.name}
                        </span>
                      </div>
                      {item.value && (
                        <span className="text-foreground font-mono font-medium tabular-nums">
                          {item.value.toLocaleString()}
                        </span>
                      )}
                    </div>
                  </>
                )}
              </div>
            )
          })}
      </div>
    </div>
  )
}

const ChartLegend = RechartsPrimitive.Legend

function ChartLegendContent({
  className,
  hideIcon = false,
  payload,
  verticalAlign = "bottom",
  nameKey,
}: React.ComponentProps<"div"> & {
    payload?: Array<{
      value?: string
      dataKey?: string | number
      color?: string
      type?: string
      payload?: unknown
    }>
    verticalAlign?: "top" | "bottom" | "middle"
    hideIcon?: boolean
    nameKey?: string
  }) {
  const { config } = useChart()

  if (!payload?.length) {
    return null
  }

  return (
    <div
      className={cn(
        "flex items-center justify-center gap-4",
        verticalAlign === "top" ? "pb-3" : "pt-3",
        className
      )}
    >
      {payload
        .filter((item) => item.type !== "none")
        .map((item) => {
          const key = `${nameKey || item.dataKey || "value"}`
          const itemConfig = getPayloadConfigFromPayload(config, item, key)

          return (
            <div
              key={item.value}
              className={cn(
                "[&>svg]:text-muted-foreground flex items-center gap-1.5 [&>svg]:h-3 [&>svg]:w-3"
              )}
            >
              {itemConfig?.icon && !hideIcon ? (
                <itemConfig.icon />
              ) : (
                <div
                  className="h-2 w-2 shrink-0 rounded-[2px]"
                  style={{
                    backgroundColor: item.color,
                  }}
                />
              )}
              {itemConfig?.label}
            </div>
          )
        })}
    </div>
  )
}

// Helper to extract item config from a payload.
function getPayloadConfigFromPayload(
  config: ChartConfig,
  payload: unknown,
  key: string
) {
  if (typeof payload !== "object" || payload === null) {
    return undefined
  }

  const payloadPayload =
    "payload" in payload &&
    typeof payload.payload === "object" &&
    payload.payload !== null
      ? payload.payload
      : undefined

  let configLabelKey: string = key

  if (
    key in payload &&
    typeof payload[key as keyof typeof payload] === "string"
  ) {
    configLabelKey = payload[key as keyof typeof payload] as string
  } else if (
    payloadPayload &&
    key in payloadPayload &&
    typeof payloadPayload[key as keyof typeof payloadPayload] === "string"
  ) {
    configLabelKey = payloadPayload[
      key as keyof typeof payloadPayload
    ] as string
  }

  return configLabelKey in config
    ? config[configLabelKey]
    : config[key as keyof typeof config]
}

export {
  ChartContainer,
  ChartTooltip,
  ChartTooltipContent,
  ChartLegend,
  ChartLegendContent,
  ChartStyle,
}

```

---

## `src\components\ui\checkbox.tsx`

```tsx
"use client"

import * as React from "react"
import * as CheckboxPrimitive from "@radix-ui/react-checkbox"
import { CheckIcon } from "lucide-react"

import { cn } from "@/lib/utils"

function Checkbox({
  className,
  ...props
}: React.ComponentProps<typeof CheckboxPrimitive.Root>) {
  return (
    <CheckboxPrimitive.Root
      data-slot="checkbox"
      className={cn(
        "peer border-input dark:bg-input/30 data-[state=checked]:bg-primary data-[state=checked]:text-primary-foreground dark:data-[state=checked]:bg-primary data-[state=checked]:border-primary focus-visible:border-ring focus-visible:ring-ring/50 aria-invalid:ring-destructive/20 dark:aria-invalid:ring-destructive/40 aria-invalid:border-destructive size-4 shrink-0 rounded-[4px] border shadow-xs transition-shadow outline-none focus-visible:ring-[3px] disabled:cursor-not-allowed disabled:opacity-50",
        className
      )}
      {...props}
    >
      <CheckboxPrimitive.Indicator
        data-slot="checkbox-indicator"
        className="grid place-content-center text-current transition-none"
      >
        <CheckIcon className="size-3.5" />
      </CheckboxPrimitive.Indicator>
    </CheckboxPrimitive.Root>
  )
}

export { Checkbox }

```

---

## `src\components\ui\circular-gallery.tsx`

```tsx
"use client";

import * as React from "react";
import { useEffect, useRef } from "react";
import gsap from "gsap";
import { cn } from "@/lib/utils";

/**
 * Circular Gallery
 *
 * A relaxing 3D ring of images: many small cards are laid out around a giant
 * tilted circle, the whole ring drifts with a gentle auto-rotation you can
 * grab and spin, and the cursor parallaxes the tilt. Hovering a card lifts it
 * and mirrors it in a large centre preview.
 *
 * Ported from the vanilla "CodeGrid 3D Circular Image Gallery" (GSAP) into a
 * single, self-contained, prop-driven React component. The original's full-page
 * ScrollTrigger is replaced with drag + auto-rotation so it works inside any
 * container. GSAP is the only runtime dependency.
 */

export interface CircularGalleryProps {
  /** Image URLs, cycled around the ring. When omitted, neutral placeholder cards are shown. */
  images?: string[];
  /** Number of cards in the ring. Defaults to 150. */
  count?: number;
  /** Base tilt of the ring in degrees (rotateX). Defaults to 55. */
  tilt?: number;
  /** Ring radius in px (card distance from centre). Defaults to 400. */
  radius?: number;
  /** Card width in px. Defaults to 45. */
  itemWidth?: number;
  /** Card height in px. Defaults to 60. */
  itemHeight?: number;
  /** Slowly spin the ring on its own. Defaults to true. */
  autoRotate?: boolean;
  /** Auto-rotation speed in degrees per second. Defaults to 4. */
  autoRotateSpeed?: number;
  /** Show the large centre preview that follows the hovered card. Defaults to true. */
  showPreview?: boolean;
  /** Parallax the ring's tilt toward the cursor. Defaults to true. */
  parallax?: boolean;
  /** Extra classes for the root element. */
  className?: string;
}

export function CircularGallery({
  images,
  count = 150,
  tilt = 55,
  radius = 400,
  itemWidth = 45,
  itemHeight = 60,
  autoRotate = true,
  autoRotateSpeed = 3,
  showPreview = true,
  parallax = true,
  className,
}: CircularGalleryProps) {
  const rootRef = useRef<HTMLDivElement>(null);
  const galleryRef = useRef<HTMLDivElement>(null);
  const previewRef = useRef<HTMLImageElement>(null);
  const previewWrapRef = useRef<HTMLDivElement>(null);

  const srcOf = (i: number) =>
    images && images.length > 0 ? images[i % images.length] : undefined;
  const defaultPreview = srcOf(0);

  // Live-tunable knobs read inside the ticker/handlers without rebuilding.
  const optsRef = useRef({ autoRotate, autoRotateSpeed, parallax, tilt });
  useEffect(() => {
    optsRef.current = { autoRotate, autoRotateSpeed, parallax, tilt };
  }, [autoRotate, autoRotateSpeed, parallax, tilt]);

  useEffect(() => {
    const root = rootRef.current;
    const gallery = galleryRef.current;
    if (!root || !gallery) return;

    const items = gsap.utils.toArray<HTMLElement>(gallery.querySelectorAll("[data-ring-item]"));
    if (items.length === 0) return;

    const angleIncrement = 360 / items.length;
    const baseAngles = items.map((_, i) => i * angleIncrement - 90);

    // Seat each card on the ring, facing outward.
    items.forEach((item, i) => {
      gsap.set(item, {
        rotationY: 90,
        rotationZ: baseAngles[i],
        transformOrigin: `50% ${radius}px`,
      });
    });
    gsap.set(gallery, { rotationY: 0 });
    // Centre preview stays hidden until a card is hovered.
    if (previewWrapRef.current) gsap.set(previewWrapRef.current, { opacity: 0 });

    const setZ = items.map((item) => gsap.quickSetter(item, "rotationZ", "deg"));

    // ── Tasteful entrance: the ring settles into its tilt, fades up, and the
    // cards bloom in softly at random. ──────────────────────────────────────
    gsap.fromTo(
      gallery,
      { rotationX: optsRef.current.tilt + 16, opacity: 0 },
      { rotationX: optsRef.current.tilt, opacity: 1, duration: 1.4, ease: "power3.out" },
    );
    gsap.from(items, {
      opacity: 0,
      duration: 0.7,
      ease: "power1.out",
      stagger: { amount: 1, from: "random" },
    });

    // ── Rotation: eased current chasing a target, nudged by auto-spin + drag ──
    let current = 0;
    let target = 0;

    const tick = () => {
      const { autoRotate: auto, autoRotateSpeed: speed } = optsRef.current;
      if (auto && !dragging) target += (speed / 60) * gsap.ticker.deltaRatio();
      current += (target - current) * 0.05;
      for (let i = 0; i < setZ.length; i++) setZ[i](baseAngles[i] + current);
    };
    gsap.ticker.add(tick);

    // ── Drag to spin ─────────────────────────────────────────────────────
    let dragging = false;
    let lastX = 0;

    const onPointerDown = (e: PointerEvent) => {
      dragging = true;
      lastX = e.clientX;
      root.setPointerCapture?.(e.pointerId);
      root.style.cursor = "grabbing";
    };
    const onPointerMove = (e: PointerEvent) => {
      // Parallax tilt toward the cursor.
      if (optsRef.current.parallax) {
        const rect = root.getBoundingClientRect();
        const px = (e.clientX - rect.left) / rect.width - 0.5;
        const py = (e.clientY - rect.top) / rect.height - 0.5;
        gsap.to(gallery, {
          rotationX: optsRef.current.tilt + py * 3,
          rotationY: px * 3,
          duration: 1.4,
          ease: "power2.out",
          overwrite: "auto",
        });
      }
      if (dragging) {
        target += (e.clientX - lastX) * 0.3;
        lastX = e.clientX;
      }
    };
    const endDrag = (e: PointerEvent) => {
      if (!dragging) return;
      dragging = false;
      root.releasePointerCapture?.(e.pointerId);
      root.style.cursor = "grab";
    };

    root.addEventListener("pointerdown", onPointerDown);
    root.addEventListener("pointermove", onPointerMove);
    root.addEventListener("pointerup", endDrag);
    root.addEventListener("pointerleave", endDrag);

    return () => {
      gsap.ticker.remove(tick);
      root.removeEventListener("pointerdown", onPointerDown);
      root.removeEventListener("pointermove", onPointerMove);
      root.removeEventListener("pointerup", endDrag);
      root.removeEventListener("pointerleave", endDrag);
      gsap.killTweensOf(gallery);
    };
    // Rebuild when structural inputs change; live knobs flow through optsRef.
  }, [count, radius, images]);

  // Reveal the centre preview with the hovered card's image — swaps instantly
  // so moving between cards feels immediate, and the frame fades in.
  const showPreviewImage = (src?: string) => {
    const img = previewRef.current;
    const wrap = previewWrapRef.current;
    if (!img || !wrap || !src) return;
    if (!img.src.endsWith(src)) img.src = src;
    gsap.to(wrap, { opacity: 1, duration: 0.15, ease: "power2.out", overwrite: true });
  };

  // Hide the preview when the cursor leaves the cards.
  const hidePreviewImage = () => {
    const wrap = previewWrapRef.current;
    if (!wrap) return;
    gsap.to(wrap, { opacity: 0, duration: 0.25, ease: "power1.out", overwrite: true });
  };

  return (
    <div
      ref={rootRef}
      className={cn(
        "relative h-full w-full touch-none select-none overflow-hidden [perspective:1500px]",
        "bg-[radial-gradient(circle_at_50%_42%,#f7f7f8,#e6e6e8)] dark:bg-[radial-gradient(circle_at_50%_42%,#17171a,#050505)]",
        className,
      )}
      style={{ cursor: "grab" }}
    >
      {/* Centre preview — hidden until a card is hovered */}
      {showPreview && defaultPreview ? (
        <div
          ref={previewWrapRef}
          className="pointer-events-none absolute left-1/2 top-1/2 z-0 h-[220px] w-[330px] max-w-[72%] -translate-x-1/2 -translate-y-1/2 overflow-hidden rounded-xl opacity-0 shadow-2xl ring-1 ring-black/10 dark:ring-white/10"
        >
          {/* eslint-disable-next-line @next/next/no-img-element */}
          <img ref={previewRef} src={defaultPreview} alt="" className="h-full w-full object-cover" />
          {/* Soft scrim for depth */}
          <div className="pointer-events-none absolute inset-0 bg-gradient-to-t from-black/25 via-transparent to-white/10" />
        </div>
      ) : null}

      {/* The ring */}
      <div
        ref={galleryRef}
        className="absolute left-1/2 top-[20%] z-10 -translate-x-1/2 [transform-style:preserve-3d]"
      >
        {Array.from({ length: count }).map((_, i) => {
          const src = srcOf(i);
          return (
            <div
              key={i}
              data-ring-item
              className="absolute left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2 overflow-hidden rounded-[3px] bg-neutral-300 shadow-md shadow-black/20 ring-1 ring-black/5 [transform-style:preserve-3d] dark:bg-neutral-700 dark:ring-white/10"
              style={{ width: itemWidth, height: itemHeight, margin: 10 }}
            >
              {src ? (
                // eslint-disable-next-line @next/next/no-img-element
                <img
                  src={src}
                  alt=""
                  onMouseEnter={() => showPreviewImage(src)}
                  onMouseLeave={hidePreviewImage}
                  className="h-full w-full object-cover transition-[transform,filter] duration-300 hover:scale-110 hover:brightness-110"
                />
              ) : null}
            </div>
          );
        })}
      </div>

      {/* Edge vignette so the ring fades softly at the periphery */}
      <div
        aria-hidden
        className="pointer-events-none absolute inset-0 z-20 bg-[radial-gradient(circle_at_50%_45%,transparent_52%,rgba(240,240,242,0.85))] dark:bg-[radial-gradient(circle_at_50%_45%,transparent_46%,rgba(5,5,5,0.9))]"
      />
    </div>
  );
}

export default CircularGallery;

```

---

## `src\components\ui\code-block.tsx`

```tsx
import * as React from "react";
import { codeToHtml } from "shiki";
import fs from "fs";
import path from "path";
import { CopyButton } from "./copy-button";

interface CodeBlockProps {
  fileName?: string; // If provided, it reads this file from src/registry/
  code?: string;     // If provided, it just highlights this string
  language?: string;
}

export async function CodeBlock({ fileName, code, language = "tsx" }: CodeBlockProps) {
  let codeString = code || "";

  // Try multiple source folders so migrated docs/components can still show code.
  if (fileName) {
    const candidates = [
      path.join(process.cwd(), "src", "registry", fileName),
      path.join(process.cwd(), "src", "components", "ui", fileName),
      path.join(process.cwd(), "src", "components", "docs", fileName),
      path.join(process.cwd(), "src", "components", "docs", "Fliptext-examples", fileName),
    ];

    try {
      const existingFilePath = candidates.find((candidatePath) => fs.existsSync(candidatePath));

      if (!existingFilePath) {
        throw new Error("No matching source file found");
      }

      codeString = fs.readFileSync(existingFilePath, "utf8");
    } catch (e) {
      codeString = `// Error reading file: ${fileName}\n// Looked in src/registry, src/components/ui, and src/components/docs`;
    }
  }

  // Convert the raw React code to styled HTML using Shiki
  const html = await codeToHtml(codeString, {
    lang: language,
    theme: "github-dark-dimmed", // Sleek, dark theme perfect for VengeanceUI
  });

  return (
    <div className="relative rounded-xl bg-[#22272e] border border-white/10 overflow-hidden my-6 shadow-2xl">
      {/* Header bar with filename and copy button */}
      <div className="flex items-center justify-between px-4 py-2 bg-[#1c2128] border-b border-white/5">
        <span className="text-xs font-mono text-zinc-400">
          {fileName || "code"}
        </span>
        <CopyButton code={codeString} />
      </div>
      
      {/* The beautifully highlighted code */}
      <div 
        className="p-4 overflow-x-auto text-sm [&>pre]:!bg-transparent"
        dangerouslySetInnerHTML={{ __html: html }}
      />
    </div>
  );
}

```

---

## `src\components\ui\collapsible.tsx`

```tsx
"use client"

import * as CollapsiblePrimitive from "@radix-ui/react-collapsible"

function Collapsible({
  ...props
}: React.ComponentProps<typeof CollapsiblePrimitive.Root>) {
  return <CollapsiblePrimitive.Root data-slot="collapsible" {...props} />
}

function CollapsibleTrigger({
  ...props
}: React.ComponentProps<typeof CollapsiblePrimitive.CollapsibleTrigger>) {
  return (
    <CollapsiblePrimitive.CollapsibleTrigger
      data-slot="collapsible-trigger"
      {...props}
    />
  )
}

function CollapsibleContent({
  ...props
}: React.ComponentProps<typeof CollapsiblePrimitive.CollapsibleContent>) {
  return (
    <CollapsiblePrimitive.CollapsibleContent
      data-slot="collapsible-content"
      {...props}
    />
  )
}

export { Collapsible, CollapsibleTrigger, CollapsibleContent }

```

---

## `src\components\ui\command.tsx`

```tsx
"use client"

import * as React from "react"
import { Command as CommandPrimitive } from "cmdk"
import { SearchIcon } from "lucide-react"

import { cn } from "@/lib/utils"
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogHeader,
  DialogTitle,
} from "@/components/ui/dialog"

function Command({
  className,
  ...props
}: React.ComponentProps<typeof CommandPrimitive>) {
  return (
    <CommandPrimitive
      data-slot="command"
      className={cn(
        "bg-popover text-popover-foreground flex h-full w-full flex-col overflow-hidden rounded-md",
        className
      )}
      {...props}
    />
  )
}

function CommandDialog({
  title = "Command Palette",
  description = "Search for a command to run...",
  children,
  className,
  ...props
}: React.ComponentProps<typeof Dialog> & {
  title?: string
  description?: string
  className?: string
}) {
  return (
    <Dialog {...props}>
      <DialogHeader className="sr-only">
        <DialogTitle>{title}</DialogTitle>
        <DialogDescription>{description}</DialogDescription>
      </DialogHeader>
      <DialogContent className={cn("overflow-hidden p-0", className)}>
        <Command className="[&_[cmdk-group-heading]]:text-muted-foreground **:data-[slot=command-input-wrapper]:h-12 [&_[cmdk-group-heading]]:px-2 [&_[cmdk-group-heading]]:font-medium [&_[cmdk-group]]:px-2 [&_[cmdk-group]:not([hidden])_~[cmdk-group]]:pt-0 [&_[cmdk-input-wrapper]_svg]:h-5 [&_[cmdk-input-wrapper]_svg]:w-5 [&_[cmdk-input]]:h-12 [&_[cmdk-item]]:px-2 [&_[cmdk-item]]:py-3 [&_[cmdk-item]_svg]:h-5 [&_[cmdk-item]_svg]:w-5">
          {children}
        </Command>
      </DialogContent>
    </Dialog>
  )
}

function CommandInput({
  className,
  ...props
}: React.ComponentProps<typeof CommandPrimitive.Input>) {
  return (
    <div
      data-slot="command-input-wrapper"
      className="flex h-9 items-center gap-2 border-b px-3 "
    >
      <SearchIcon className="size-4 shrink-0 opacity-50" />
      <CommandPrimitive.Input
        data-slot="command-input"
        className={cn(
          "placeholder:text-muted-foreground flex h-10 w-full rounded-md bg-transparent py-3 text-sm outline-hidden disabled:cursor-not-allowed disabled:opacity-50",
          className
        )}
        {...props}
      />
    </div>
  )
}

function CommandList({
  className,
  ...props
}: React.ComponentProps<typeof CommandPrimitive.List>) {
  return (
    <CommandPrimitive.List
      data-slot="command-list"
      className={cn(
        "max-h-[300px] scroll-py-1 overflow-x-hidden overflow-y-auto",
        className
      )}
      {...props}
    />
  )
}

function CommandEmpty({
  ...props
}: React.ComponentProps<typeof CommandPrimitive.Empty>) {
  return (
    <CommandPrimitive.Empty
      data-slot="command-empty"
      className="py-6 text-center text-sm"
      {...props}
    />
  )
}

function CommandGroup({
  className,
  ...props
}: React.ComponentProps<typeof CommandPrimitive.Group>) {
  return (
    <CommandPrimitive.Group
      data-slot="command-group"
      className={cn(
        "text-foreground [&_[cmdk-group-heading]]:text-muted-foreground overflow-hidden p-1 [&_[cmdk-group-heading]]:px-2 [&_[cmdk-group-heading]]:py-1.5 [&_[cmdk-group-heading]]:text-xs [&_[cmdk-group-heading]]:font-medium",
        className
      )}
      {...props}
    />
  )
}

function CommandSeparator({
  className,
  ...props
}: React.ComponentProps<typeof CommandPrimitive.Separator>) {
  return (
    <CommandPrimitive.Separator
      data-slot="command-separator"
      className={cn("bg-border -mx-1 h-px", className)}
      {...props}
    />
  )
}

function CommandItem({
  className,
  ...props
}: React.ComponentProps<typeof CommandPrimitive.Item>) {
  return (
    <CommandPrimitive.Item
      data-slot="command-item"
      className={cn(
        "data-[selected=true]:bg-accent data-[selected=true]:text-accent-foreground [&_svg:not([class*='text-'])]:text-muted-foreground relative flex cursor-default items-center gap-2 rounded-sm px-2 py-1.5 text-sm outline-hidden select-none data-[disabled=true]:pointer-events-none data-[disabled=true]:opacity-50 [&_svg]:pointer-events-none [&_svg]:shrink-0 [&_svg:not([class*='size-'])]:size-4",
        className
      )}
      {...props}
    />
  )
}

function CommandShortcut({
  className,
  ...props
}: React.ComponentProps<"span">) {
  return (
    <span
      data-slot="command-shortcut"
      className={cn(
        "text-muted-foreground ml-auto text-xs tracking-widest",
        className
      )}
      {...props}
    />
  )
}

export {
  Command,
  CommandDialog,
  CommandInput,
  CommandList,
  CommandEmpty,
  CommandGroup,
  CommandItem,
  CommandShortcut,
  CommandSeparator,
}

```

---

## `src\components\ui\component-preview-panel.tsx`

```tsx
"use client";

import * as React from "react";
import { Maximize2, Minimize2, PictureInPicture2, Terminal, TerminalSquare } from "lucide-react";
import { CopyButton } from "@/components/ui/copy-button";
import { TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { cn } from "@/lib/utils";

interface ComponentPreviewPanelProps {
  installCommand: string;
  children: React.ReactNode;
}

export function ComponentPreviewPanel({ installCommand, children }: ComponentPreviewPanelProps) {
  const [isFullscreen, setIsFullscreen] = React.useState(false);
  const stageRef = React.useRef<HTMLDivElement>(null);

  React.useEffect(() => {
    if (!isFullscreen) return;

    const previousOverflow = document.body.style.overflow;
    const onKeyDown = (event: KeyboardEvent) => {
      if (event.key === "Escape") {
        setIsFullscreen(false);
      }
    };

    document.body.style.overflow = "hidden";
    window.addEventListener("keydown", onKeyDown);
    stageRef.current?.focus({ preventScroll: true });

    return () => {
      document.body.style.overflow = previousOverflow;
      window.removeEventListener("keydown", onKeyDown);
    };
  }, [isFullscreen]);

  React.useEffect(() => {
    const animationFrame = window.requestAnimationFrame(() => {
      window.dispatchEvent(new Event("resize"));
    });

    return () => window.cancelAnimationFrame(animationFrame);
  }, [isFullscreen]);

  return (
    <>
      <div className="flex flex-col gap-3 lg:flex-row lg:items-center lg:justify-between">
        <TabsList className="mb-0">
          <TabsTrigger value="preview" className="gap-2 px-3 py-1.5 text-sm h-8 font-medium">
            <PictureInPicture2 className="h-4 w-4" />
            Preview
          </TabsTrigger>
          <TabsTrigger value="code" className="gap-2 px-3 py-1.5 text-sm h-8 font-medium text-neutral-500 dark:text-zinc-400 hover:text-neutral-700 dark:hover:text-zinc-300">
            <TerminalSquare className="h-4 w-4" />
            Code
          </TabsTrigger>
        </TabsList>

        <div className="flex w-full min-w-0 items-center gap-2 rounded-xl border border-neutral-200 dark:border-white/10 bg-neutral-50 dark:bg-black px-1.5 py-1.5 text-xs text-neutral-600 dark:text-zinc-300 shadow-sm dark:shadow-[inset_0_1px_0_rgba(255,255,255,0.05)] lg:w-auto">
          <div className="inline-flex h-6 w-6 shrink-0 items-center justify-center rounded-md bg-neutral-100 dark:bg-zinc-800 text-neutral-400 dark:text-zinc-500">
            <Terminal className="h-3.5 w-3.5" />
          </div>
          <span className="min-w-0 max-w-[42vw] truncate font-mono text-neutral-500 dark:text-zinc-400 lg:max-w-[500px]">{installCommand}</span>
          <CopyButton code={installCommand} className="ml-auto h-7 w-7 shrink-0 border-neutral-200 dark:border-white/10 bg-transparent hover:bg-neutral-100 dark:hover:bg-white/10" />
          <button
            type="button"
            onClick={() => setIsFullscreen(true)}
            className="inline-flex h-7 w-7 shrink-0 items-center justify-center rounded-md border border-neutral-200 dark:border-white/10 bg-transparent text-neutral-400 dark:text-zinc-400 transition-colors hover:bg-neutral-100 dark:hover:bg-white/10 hover:text-neutral-700 dark:hover:text-zinc-200"
            aria-label="Expand to fullscreen"
          >
            <Maximize2 className="h-3.5 w-3.5" />
          </button>
        </div>
      </div>

      <TabsContent value="preview">
        <div className="w-full">
          <div
            id="preview"
            className="w-full scroll-mt-24 rounded-2xl border border-neutral-200 dark:border-[#222] bg-neutral-100 dark:bg-zinc-900 p-2.5 shadow-lg dark:shadow-[0_20px_60px_rgba(0,0,0,0.45)] sm:p-4"
          >
            <div
              ref={stageRef}
              tabIndex={isFullscreen ? -1 : undefined}
              role={isFullscreen ? "dialog" : undefined}
              aria-modal={isFullscreen ? true : undefined}
              aria-label={isFullscreen ? "Fullscreen preview" : undefined}
              data-fullscreen-preview-surface
              className={cn(
                "relative flex h-[680px] items-stretch overflow-hidden rounded-xl border border-neutral-200 dark:border-[#222] bg-white dark:bg-black outline-none",
                isFullscreen && "fixed inset-0 z-[9999] h-[100dvh] w-[100dvw] rounded-none border-0"
              )}
            >
              <button
                type="button"
                onClick={() => setIsFullscreen(false)}
                className={cn(
                  "absolute right-3 top-3 z-[10000] inline-flex h-9 w-9 items-center justify-center rounded-md border border-white/10 bg-black/70 text-white/70 shadow-lg backdrop-blur transition-colors hover:bg-black/85 hover:text-white focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-white/60",
                  !isFullscreen && "hidden"
                )}
                aria-label="Exit fullscreen"
              >
                <Minimize2 className="h-4 w-4" />
              </button>

              <div className="pointer-events-none absolute inset-x-0 top-0 h-px bg-black/5 dark:bg-white/10" />
              <div className="absolute inset-0 bg-[radial-gradient(circle_at_20%_0%,rgba(0,0,0,0.03),transparent_32%),radial-gradient(circle_at_80%_100%,rgba(0,0,0,0.02),transparent_34%)] dark:bg-[radial-gradient(circle_at_20%_0%,rgba(255,255,255,0.08),transparent_32%),radial-gradient(circle_at_80%_100%,rgba(255,255,255,0.04),transparent_34%)]" />

              <div className="relative z-10 w-full h-full flex justify-center items-center">
                {children}
              </div>
            </div>
          </div>
        </div>
      </TabsContent>
    </>
  );
}

```

---

## `src\components\ui\component-showcase.tsx`

```tsx
import * as React from "react";
import fs from "fs";
import path from "path";
import { CodeBlock } from "@/components/ui/code-block";
import { Tabs, TabsContent } from "@/components/ui/tabs";
import { ComponentDocsSections } from "@/components/docs/component-docs-sections";
import { ComponentPreviewPanel } from "@/components/ui/component-preview-panel";
import { getShadcnAddCommand } from "@/lib/registry";

interface ComponentShowcaseProps {
  componentName: string; // The exact filename in the registry (without .tsx)
  title: string;
  description: string;
  slug?: string;
  children: React.ReactNode; // The live component itself
}

/**
 * Read the component source code from the filesystem (server-side only).
 * Tries multiple candidate directories.
 * The turbopackIgnore comment prevents Turbopack from tracing the entire project.
 */
function readComponentSource(componentName: string): string {
  const fileName = `${componentName}.tsx`;

  try {
    const p1 = path.join(process.cwd(), "src", "components", "ui", fileName);
    if (fs.existsSync(p1)) return fs.readFileSync(p1, "utf8");
  } catch {}

  try {
    const p2 = path.join(process.cwd(), "src", "registry", fileName);
    if (fs.existsSync(p2)) return fs.readFileSync(p2, "utf8");
  } catch {}

  try {
    const p3 = path.join(process.cwd(), "src", "components", "docs", fileName);
    if (fs.existsSync(p3)) return fs.readFileSync(p3, "utf8");
  } catch {}

  return `// Source code for ${componentName} not found`;
}

export function ComponentShowcase({
  componentName,
  title,
  description,
  slug = componentName,
  children,
}: ComponentShowcaseProps) {
  const installCommand = getShadcnAddCommand(componentName);
  const sourceCode = readComponentSource(componentName);

  return (
    <div className="mb-8 space-y-4">
      {/* Component Header */}
      <div id="overview" className="space-y-1 scroll-mt-24">
        <p className="text-sm font-medium text-neutral-500 dark:text-zinc-500">
          Components <span className="mx-1 text-neutral-400 dark:text-zinc-700">/</span>
          <span className="text-neutral-900 dark:text-zinc-200">{title}</span>
        </p>
        <h1 className="mb-2 text-3xl font-bold tracking-tight text-neutral-900 dark:text-zinc-100">{title}</h1>
        <p className="text-neutral-500 dark:text-zinc-400 text-lg">{description}</p>
      </div>

      {/* The Showcase Toggle */}
      <Tabs defaultValue="preview" className="space-y-4">
        <ComponentPreviewPanel installCommand={installCommand}>
          {children}
        </ComponentPreviewPanel>

        {/* Code Block */}
        <TabsContent value="code">
          <div id="code" className="scroll-mt-24" />
          <div className="mt-4">
            <CodeBlock fileName={`${componentName}.tsx`} />
          </div>
        </TabsContent>
      </Tabs>

      {/* ─── Documentation Sections (Client Component) ─── */}
      <ComponentDocsSections componentName={componentName} slug={slug} sourceCode={sourceCode} />
    </div>
  );
}

```

---

## `src\components\ui\context-menu.tsx`

```tsx
"use client"

import * as React from "react"
import * as ContextMenuPrimitive from "@radix-ui/react-context-menu"
import { CheckIcon, ChevronRightIcon, CircleIcon } from "lucide-react"

import { cn } from "@/lib/utils"

function ContextMenu({
  ...props
}: React.ComponentProps<typeof ContextMenuPrimitive.Root>) {
  return <ContextMenuPrimitive.Root data-slot="context-menu" {...props} />
}

function ContextMenuTrigger({
  ...props
}: React.ComponentProps<typeof ContextMenuPrimitive.Trigger>) {
  return (
    <ContextMenuPrimitive.Trigger data-slot="context-menu-trigger" {...props} />
  )
}

function ContextMenuGroup({
  ...props
}: React.ComponentProps<typeof ContextMenuPrimitive.Group>) {
  return (
    <ContextMenuPrimitive.Group data-slot="context-menu-group" {...props} />
  )
}

function ContextMenuPortal({
  ...props
}: React.ComponentProps<typeof ContextMenuPrimitive.Portal>) {
  return (
    <ContextMenuPrimitive.Portal data-slot="context-menu-portal" {...props} />
  )
}

function ContextMenuSub({
  ...props
}: React.ComponentProps<typeof ContextMenuPrimitive.Sub>) {
  return <ContextMenuPrimitive.Sub data-slot="context-menu-sub" {...props} />
}

function ContextMenuRadioGroup({
  ...props
}: React.ComponentProps<typeof ContextMenuPrimitive.RadioGroup>) {
  return (
    <ContextMenuPrimitive.RadioGroup
      data-slot="context-menu-radio-group"
      {...props}
    />
  )
}

function ContextMenuSubTrigger({
  className,
  inset,
  children,
  ...props
}: React.ComponentProps<typeof ContextMenuPrimitive.SubTrigger> & {
  inset?: boolean
}) {
  return (
    <ContextMenuPrimitive.SubTrigger
      data-slot="context-menu-sub-trigger"
      data-inset={inset}
      className={cn(
        "focus:bg-accent focus:text-accent-foreground data-[state=open]:bg-accent data-[state=open]:text-accent-foreground [&_svg:not([class*='text-'])]:text-muted-foreground flex cursor-default items-center rounded-sm px-2 py-1.5 text-sm outline-hidden select-none data-[inset]:pl-8 [&_svg]:pointer-events-none [&_svg]:shrink-0 [&_svg:not([class*='size-'])]:size-4",
        className
      )}
      {...props}
    >
      {children}
      <ChevronRightIcon className="ml-auto" />
    </ContextMenuPrimitive.SubTrigger>
  )
}

function ContextMenuSubContent({
  className,
  ...props
}: React.ComponentProps<typeof ContextMenuPrimitive.SubContent>) {
  return (
    <ContextMenuPrimitive.SubContent
      data-slot="context-menu-sub-content"
      className={cn(
        "bg-popover text-popover-foreground data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0 data-[state=closed]:zoom-out-95 data-[state=open]:zoom-in-95 data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-from-right-2 data-[side=right]:slide-in-from-left-2 data-[side=top]:slide-in-from-bottom-2 z-50 min-w-[8rem] origin-(--radix-context-menu-content-transform-origin) overflow-hidden rounded-md border p-1 shadow-lg",
        className
      )}
      {...props}
    />
  )
}

function ContextMenuContent({
  className,
  ...props
}: React.ComponentProps<typeof ContextMenuPrimitive.Content>) {
  return (
    <ContextMenuPrimitive.Portal>
      <ContextMenuPrimitive.Content
        data-slot="context-menu-content"
        className={cn(
          "bg-popover text-popover-foreground data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0 data-[state=closed]:zoom-out-95 data-[state=open]:zoom-in-95 data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-from-right-2 data-[side=right]:slide-in-from-left-2 data-[side=top]:slide-in-from-bottom-2 z-50 max-h-(--radix-context-menu-content-available-height) min-w-[8rem] origin-(--radix-context-menu-content-transform-origin) overflow-x-hidden overflow-y-auto rounded-md border p-1 shadow-md",
          className
        )}
        {...props}
      />
    </ContextMenuPrimitive.Portal>
  )
}

function ContextMenuItem({
  className,
  inset,
  variant = "default",
  ...props
}: React.ComponentProps<typeof ContextMenuPrimitive.Item> & {
  inset?: boolean
  variant?: "default" | "destructive"
}) {
  return (
    <ContextMenuPrimitive.Item
      data-slot="context-menu-item"
      data-inset={inset}
      data-variant={variant}
      className={cn(
        "focus:bg-accent focus:text-accent-foreground data-[variant=destructive]:text-destructive data-[variant=destructive]:focus:bg-destructive/10 dark:data-[variant=destructive]:focus:bg-destructive/20 data-[variant=destructive]:focus:text-destructive data-[variant=destructive]:*:[svg]:!text-destructive [&_svg:not([class*='text-'])]:text-muted-foreground relative flex cursor-default items-center gap-2 rounded-sm px-2 py-1.5 text-sm outline-hidden select-none data-[disabled]:pointer-events-none data-[disabled]:opacity-50 data-[inset]:pl-8 [&_svg]:pointer-events-none [&_svg]:shrink-0 [&_svg:not([class*='size-'])]:size-4",
        className
      )}
      {...props}
    />
  )
}

function ContextMenuCheckboxItem({
  className,
  children,
  checked,
  ...props
}: React.ComponentProps<typeof ContextMenuPrimitive.CheckboxItem>) {
  return (
    <ContextMenuPrimitive.CheckboxItem
      data-slot="context-menu-checkbox-item"
      className={cn(
        "focus:bg-accent focus:text-accent-foreground relative flex cursor-default items-center gap-2 rounded-sm py-1.5 pr-2 pl-8 text-sm outline-hidden select-none data-[disabled]:pointer-events-none data-[disabled]:opacity-50 [&_svg]:pointer-events-none [&_svg]:shrink-0 [&_svg:not([class*='size-'])]:size-4",
        className
      )}
      checked={checked}
      {...props}
    >
      <span className="pointer-events-none absolute left-2 flex size-3.5 items-center justify-center">
        <ContextMenuPrimitive.ItemIndicator>
          <CheckIcon className="size-4" />
        </ContextMenuPrimitive.ItemIndicator>
      </span>
      {children}
    </ContextMenuPrimitive.CheckboxItem>
  )
}

function ContextMenuRadioItem({
  className,
  children,
  ...props
}: React.ComponentProps<typeof ContextMenuPrimitive.RadioItem>) {
  return (
    <ContextMenuPrimitive.RadioItem
      data-slot="context-menu-radio-item"
      className={cn(
        "focus:bg-accent focus:text-accent-foreground relative flex cursor-default items-center gap-2 rounded-sm py-1.5 pr-2 pl-8 text-sm outline-hidden select-none data-[disabled]:pointer-events-none data-[disabled]:opacity-50 [&_svg]:pointer-events-none [&_svg]:shrink-0 [&_svg:not([class*='size-'])]:size-4",
        className
      )}
      {...props}
    >
      <span className="pointer-events-none absolute left-2 flex size-3.5 items-center justify-center">
        <ContextMenuPrimitive.ItemIndicator>
          <CircleIcon className="size-2 fill-current" />
        </ContextMenuPrimitive.ItemIndicator>
      </span>
      {children}
    </ContextMenuPrimitive.RadioItem>
  )
}

function ContextMenuLabel({
  className,
  inset,
  ...props
}: React.ComponentProps<typeof ContextMenuPrimitive.Label> & {
  inset?: boolean
}) {
  return (
    <ContextMenuPrimitive.Label
      data-slot="context-menu-label"
      data-inset={inset}
      className={cn(
        "text-foreground px-2 py-1.5 text-sm font-medium data-[inset]:pl-8",
        className
      )}
      {...props}
    />
  )
}

function ContextMenuSeparator({
  className,
  ...props
}: React.ComponentProps<typeof ContextMenuPrimitive.Separator>) {
  return (
    <ContextMenuPrimitive.Separator
      data-slot="context-menu-separator"
      className={cn("bg-border -mx-1 my-1 h-px", className)}
      {...props}
    />
  )
}

function ContextMenuShortcut({
  className,
  ...props
}: React.ComponentProps<"span">) {
  return (
    <span
      data-slot="context-menu-shortcut"
      className={cn(
        "text-muted-foreground ml-auto text-xs tracking-widest",
        className
      )}
      {...props}
    />
  )
}

export {
  ContextMenu,
  ContextMenuTrigger,
  ContextMenuContent,
  ContextMenuItem,
  ContextMenuCheckboxItem,
  ContextMenuRadioItem,
  ContextMenuLabel,
  ContextMenuSeparator,
  ContextMenuShortcut,
  ContextMenuGroup,
  ContextMenuPortal,
  ContextMenuSub,
  ContextMenuSubContent,
  ContextMenuSubTrigger,
  ContextMenuRadioGroup,
}

```

---

## `src\components\ui\copy-button.tsx`

```tsx
"use client";

import * as React from "react";
import { Check, Copy } from "lucide-react";
import { cn } from "@/lib/utils";

export function CopyButton({ code, className }: { code: string; className?: string }) {
  const [hasCopied, setHasCopied] = React.useState(false);

  React.useEffect(() => {
    if (!hasCopied) return;
    const timeout = setTimeout(() => {
      setHasCopied(false);
    }, 2000);
    return () => clearTimeout(timeout);
  }, [hasCopied]);

  return (
    <button
      className={cn(
        "relative z-10 flex h-7 w-7 items-center justify-center rounded-md border bg-zinc-900 border-zinc-800 text-zinc-400 hover:text-neutral-700 hover:bg-zinc-800 dark:hover:text-zinc-50 transition-colors",
        className
      )}
      onClick={() => {
        navigator.clipboard.writeText(code);
        setHasCopied(true);
      }}
    >
      <span className="sr-only">Copy</span>
      {hasCopied ? (
        <Check className="h-3.5 w-3.5 text-emerald-400" />
      ) : (
        <Copy className="h-3.5 w-3.5" />
      )}
    </button>
  );
}

```

---

## `src\components\ui\corner-button.tsx`

```tsx
"use client";

import React from "react";
import { cn } from "@/lib/utils";

// ─── Types ────────────────────────────────────────────────────────────────────

export interface CornerButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  /** Icon rendered to the right of the label. Defaults to the pencil/design SVG. */
  icon?: React.ReactNode;
  /** Show the default pencil icon. Set to false to hide it entirely. @default true */
  showIcon?: boolean;
  /** Accent colour used for the button background and glow. @default "#e5ff00" */
  accentColor?: string;
  /** Extra classes applied to the outer wrapper div. */
  wrapperClassName?: string;
}

// ─── Default icon ─────────────────────────────────────────────────────────────

const PencilIcon = ({ className }: { className?: string }) => (
  <svg
    className={className}
    viewBox="0 0 24 24"
    xmlns="http://www.w3.org/2000/svg"
    strokeLinecap="round"
    strokeLinejoin="round"
  >
    <path d="M17.6744 11.4075L15.7691 17.1233C15.7072 17.309 15.5586 17.4529 15.3709 17.5087L3.69348 20.9803C3.22819 21.1186 2.79978 20.676 2.95328 20.2155L6.74467 8.84131C6.79981 8.67588 6.92419 8.54263 7.08543 8.47624L12.472 6.25822C12.696 6.166 12.9535 6.21749 13.1248 6.38876L17.5294 10.7935C17.6901 10.9542 17.7463 11.1919 17.6744 11.4075Z" />
    <path d="M3.2959 20.6016L9.65986 14.2376" />
    <path d="M17.7917 11.0557L20.6202 8.22724C21.4012 7.44619 21.4012 6.17986 20.6202 5.39881L18.4989 3.27749C17.7178 2.49645 16.4515 2.49645 15.6704 3.27749L12.842 6.10592" />
    <path d="M11.7814 12.1163C11.1956 11.5305 10.2458 11.5305 9.66004 12.1163C9.07426 12.7021 9.07426 13.6519 9.66004 14.2376C10.2458 14.8234 11.1956 14.8234 11.7814 14.2376C12.3671 13.6519 12.3671 12.7021 11.7814 12.1163Z" />
  </svg>
);

// ─── Component ────────────────────────────────────────────────────────────────

export function CornerButton({
  children = "Start designing",
  icon,
  showIcon = true,
  accentColor = "#e5ff00",
  className,
  wrapperClassName,
  style,
  ...props
}: CornerButtonProps) {
  const resolvedIcon =
    icon ??
    (showIcon ? (
      <PencilIcon className="corner-btn-svg" />
    ) : null);

  return (
    <div
      className={cn("corner-btn-wrapper", wrapperClassName)}
      style={
        {
          "--accent": accentColor,
          "--accent-glow": `${accentColor}55`,
        } as React.CSSProperties
      }
    >
      {/* Animated corner lines */}
      <div className="corner-line horizontal top" aria-hidden="true" />
      <div className="corner-line vertical right" aria-hidden="true" />
      <div className="corner-line horizontal bottom" aria-hidden="true" />
      <div className="corner-line vertical left" aria-hidden="true" />

      {/* Animated corner dots */}
      <div className="corner-dot top left" aria-hidden="true" />
      <div className="corner-dot top right" aria-hidden="true" />
      <div className="corner-dot bottom right" aria-hidden="true" />
      <div className="corner-dot bottom left" aria-hidden="true" />

      <button
        className={cn("corner-btn", className)}
        style={style}
        {...props}
      >
        <span className="corner-btn-text">{children}</span>
        {resolvedIcon}
      </button>

      {/* Scoped styles — no global CSS pollution */}
      <style>{`
        .corner-btn-wrapper {
          --dot-size: 6px;
          --line-weight: 1px;
          --padding: 0.9rem 1.1rem;
          --speed: 0.35s;
          --dot-color: #666;
          --line-color: #999;

          position: relative;
          display: inline-flex;
          justify-content: center;
          align-items: center;
          padding: var(--padding);
          background-color: transparent;
          transition: background-color 0.3s ease-in-out;
          user-select: none;
        }

        .corner-btn-wrapper:has(.corner-btn:hover) {
          animation: corner-bg-change calc(var(--speed) * 4) ease-in-out forwards;
        }
        @keyframes corner-bg-change {
          80%  { background-color: transparent; }
          100% { background-color: var(--accent-glow); }
        }

        /* ── Button ───────────────────────── */
        .corner-btn {
          position: relative;
          display: inline-flex;
          justify-content: center;
          align-items: center;
          gap: 0.5rem;
          padding: 0.8rem 1.25rem;
          background-color: var(--accent);
          background-image: linear-gradient(#0000, #0004);
          border: none;
          color: #0008;
          font-family: "Inter", sans-serif;
          font-size: 1rem;
          font-weight: 600;
          text-transform: capitalize;
          border-radius: 30% / 200%;
          cursor: pointer;
          box-shadow:
            0 0 0px 1px #0003,
            0px 1px 1px rgba(3,7,18,.02),
            0px 5px 4px rgba(3,7,18,.04),
            0px 12px 9px rgba(3,7,18,.06),
            0px 20px 15px rgba(3,7,18,.08),
            0px 32px 24px rgba(3,7,18,.1);
          transition:
            background-color 0.2s ease-in-out,
            transform 0.2s ease-in-out,
            box-shadow 0.2s ease-in-out,
            border-radius 0.3s ease-in-out;
        }
        .corner-btn:hover {
          background-color: #fff;
          transform: scale(1.05);
          border-radius: 10% / 200%;
        }
        .corner-btn:active {
          background-color: var(--accent);
          transform: scale(0.98);
          border-radius: 20% / 200%;
        }
        .corner-btn:disabled {
          opacity: 0.5;
          cursor: not-allowed;
          pointer-events: none;
        }

        /* ── Icon ─────────────────────────── */
        .corner-btn-svg {
          height: 24px;
          width: 24px;
          flex-shrink: 0;
          stroke-width: 1;
          stroke-linecap: round;
          stroke-linejoin: round;
          stroke: #0007;
          fill: #fffa;
          transition: all 0.3s ease-in-out;
        }
        .corner-btn:hover .corner-btn-svg {
          stroke: #0008;
          fill: var(--accent);
        }
        .corner-btn:active .corner-btn-svg {
          stroke: #0009;
          fill: color-mix(in srgb, var(--accent) 80%, white);
        }

        /* ── Dots ─────────────────────────── */
        .corner-dot {
          position: absolute;
          width: var(--dot-size);
          aspect-ratio: 1;
          border-radius: 50%;
          background-color: var(--dot-color);
          opacity: 0;
          transition: all 0.3s ease-in-out;
        }
        .corner-btn-wrapper:has(.corner-btn:hover) .corner-dot.top.left {
          top: 50%; left: 20%;
          animation: corner-dot-tl var(--speed) ease-in-out forwards;
        }
        @keyframes corner-dot-tl {
          90%  { opacity: 0.6; }
          100% { top: calc(var(--dot-size) * -0.5); left: calc(var(--dot-size) * -0.5); opacity: 1; }
        }
        .corner-btn-wrapper:has(.corner-btn:hover) .corner-dot.top.right {
          top: 50%; right: 20%;
          animation: corner-dot-tr var(--speed) ease-in-out forwards;
          animation-delay: calc(var(--speed) * 0.6);
        }
        @keyframes corner-dot-tr {
          80%  { opacity: 0.6; }
          100% { top: calc(var(--dot-size) * -0.5); right: calc(var(--dot-size) * -0.5); opacity: 1; }
        }
        .corner-btn-wrapper:has(.corner-btn:hover) .corner-dot.bottom.right {
          bottom: 50%; right: 20%;
          animation: corner-dot-br var(--speed) ease-in-out forwards;
          animation-delay: calc(var(--speed) * 1.2);
        }
        @keyframes corner-dot-br {
          80%  { opacity: 0.6; }
          100% { bottom: calc(var(--dot-size) * -0.5); right: calc(var(--dot-size) * -0.5); opacity: 1; }
        }
        .corner-btn-wrapper:has(.corner-btn:hover) .corner-dot.bottom.left {
          bottom: 50%; left: 20%;
          animation: corner-dot-bl var(--speed) ease-in-out forwards;
          animation-delay: calc(var(--speed) * 1.8);
        }
        @keyframes corner-dot-bl {
          80%  { opacity: 0.6; }
          100% { bottom: calc(var(--dot-size) * -0.5); left: calc(var(--dot-size) * -0.5); opacity: 1; }
        }

        /* ── Lines ────────────────────────── */
        .corner-line {
          position: absolute;
          transition: all 0.3s ease-in-out;
        }
        .corner-line.horizontal {
          height: var(--line-weight);
          width: 100%;
          background-image: repeating-linear-gradient(
            90deg,
            #0000 0 calc(var(--line-weight) * 2),
            var(--line-color) calc(var(--line-weight) * 2) calc(var(--line-weight) * 4)
          );
        }
        .corner-line.vertical {
          width: var(--line-weight);
          height: 100%;
          background-image: repeating-linear-gradient(
            0deg,
            #0000 0 calc(var(--line-weight) * 2),
            var(--line-color) calc(var(--line-weight) * 2) calc(var(--line-weight) * 4)
          );
        }
        .corner-line.top    { top:    calc(var(--line-weight) * -0.5); transform-origin: top left;    transform: rotate(5deg) scaleX(0); }
        .corner-line.bottom { bottom: calc(var(--line-weight) * -0.5); transform-origin: bottom right; transform: rotate(5deg) scaleX(0); }
        .corner-line.left   { left:   calc(var(--line-weight) * -0.5); transform-origin: bottom left;  transform: scaleY(0); }
        .corner-line.right  { right:  calc(var(--line-weight) * -0.5); transform-origin: top right;    transform: rotate(5deg) scaleY(0); }

        .corner-btn-wrapper:has(.corner-btn:hover) .corner-line.top {
          animation: corner-line-top var(--speed) ease-in-out forwards;
          animation-delay: calc(var(--speed) * 0.8);
        }
        @keyframes corner-line-top    { 100% { transform: rotate(0deg) scaleX(1); } }

        .corner-btn-wrapper:has(.corner-btn:hover) .corner-line.bottom {
          animation: corner-line-bottom var(--speed) ease-in-out forwards;
          animation-delay: calc(var(--speed) * 2);
        }
        @keyframes corner-line-bottom { 100% { transform: rotate(0deg) scaleX(1); } }

        .corner-btn-wrapper:has(.corner-btn:hover) .corner-line.left {
          animation: corner-line-left var(--speed) ease-in-out forwards;
          animation-delay: calc(var(--speed) * 2.4);
        }
        @keyframes corner-line-left   { 100% { transform: scaleY(1); } }

        .corner-btn-wrapper:has(.corner-btn:hover) .corner-line.right {
          animation: corner-line-right var(--speed) ease-in-out forwards;
          animation-delay: calc(var(--speed) * 1.4);
        }
        @keyframes corner-line-right  { 100% { transform: rotate(0deg) scaleY(1); } }
      `}</style>
    </div>
  );
}

export default CornerButton;

```

---

## `src\components\ui\creepy-button.tsx`

```tsx
"use client";

import React, { useRef, useState } from "react";
import { motion } from "framer-motion";
import { cn } from "@/lib/utils";

interface CreepyButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
    children: React.ReactNode;
    /**
     * Optional custom class for the button container
     */
    className?: string;
    /**
     * Optional custom class for the button cover (the visible part)
     */
    coverClassName?: string;
}

type Coords = {
    x: number;
    y: number;
};

export const CreepyButton = ({
    children,
    className,
    coverClassName,
    onClick,
    ...props
}: CreepyButtonProps) => {
    const eyesRef = useRef<HTMLSpanElement>(null);
    const [eyeCoords, setEyeCoords] = useState<Coords>({ x: 0, y: 0 });
    const [isHovered, setIsHovered] = useState(false);

    const updateEyes = (e: React.MouseEvent | React.TouchEvent) => {
        const userEvent =
            "touches" in e ? (e as React.TouchEvent).touches[0] : (e as React.MouseEvent);

        if (!eyesRef.current) return;

        // get the center of the eyes container
        const eyesRect = eyesRef.current.getBoundingClientRect();
        const eyesCenter = {
            x: eyesRect.left + eyesRect.width / 2,
            y: eyesRect.top + eyesRect.height / 2,
        };

        // cursor position
        const cursor = {
            x: userEvent.clientX,
            y: userEvent.clientY,
        };

        // calculate the eye angle
        const dx = cursor.x - eyesCenter.x;
        const dy = cursor.y - eyesCenter.y;
        const angle = Math.atan2(-dy, dx) + Math.PI / 2;

        // pupil distance from the eye center
        const visionRangeX = 180; // Max distance to look horizontally
        const visionRangeY = 75; // Max distance to look vertically
        const distance = Math.hypot(dx, dy);

        // Limit the movement so pupils don't go too far
        // We normalize the distance influence
        const x = (Math.sin(angle) * Math.min(distance, visionRangeX)) / visionRangeX;
        const y = (Math.cos(angle) * Math.min(distance, visionRangeY)) / visionRangeY;

        setEyeCoords({ x, y });
    };

    // Reset eyes when mouse leaves
    const resetEyes = () => {
        setEyeCoords({ x: 0, y: 0 });
        setIsHovered(false);
    };

    const pupilStyle = {
        transform: `translate(calc(-50% + ${eyeCoords.x * 50}%), calc(-50% + ${eyeCoords.y * 50}%))`,
    };

    return (
        <button
            className={cn(
                "relative min-w-[9em] rounded-xl bg-black cursor-pointer outline-none select-none group tap-highlight-transparent",
                "focus-visible:ring-2 focus-visible:ring-offset-2 focus-visible:ring-blue-400",
                className
            )}
            onClick={onClick}
            onMouseMove={(e) => {
                updateEyes(e);
                setIsHovered(true);
            }}
            onTouchMove={updateEyes}
            onMouseLeave={resetEyes}
            onFocus={() => setIsHovered(true)}
            onBlur={() => setIsHovered(false)}
            {...props}
        >
            {/* Eyes Container */}
            <span
                ref={eyesRef}
                className="absolute flex items-center gap-[0.375em] right-[1em] bottom-[0.5em] h-[0.75em] z-0 pointer-events-none"
            >
                {/* Left Eye */}
                <motion.span
                    className="relative w-[0.75em] bg-white rounded-full overflow-hidden"
                    animate={{ height: ["0.75em", "0.75em", "0em", "0.75em"] }}
                    transition={{
                        duration: 3,
                        times: [0, 0.92, 0.96, 1],
                        repeat: Infinity,
                        ease: "linear",
                    }}
                >
                    <span
                        className="absolute top-1/2 left-1/2 w-[0.375em] h-[0.375em] bg-black rounded-full transition-transform duration-75 ease-out"
                        style={pupilStyle}
                    />
                </motion.span>
                {/* Right Eye */}
                <motion.span
                    className="relative w-[0.75em] bg-white rounded-full overflow-hidden"
                    animate={{ height: ["0.75em", "0.75em", "0em", "0.75em"] }}
                    transition={{
                        duration: 3,
                        times: [0, 0.92, 0.96, 1],
                        repeat: Infinity,
                        ease: "linear",
                    }}
                >
                    <span
                        className="absolute top-1/2 left-1/2 w-[0.375em] h-[0.375em] bg-black rounded-full transition-transform duration-75 ease-out"
                        style={pupilStyle}
                    />
                </motion.span>
            </span>

            {/* Button Cover */}
            <motion.span
                className={cn(
                    "absolute inset-0 block rounded-xl bg-blue-500 text-white font-bold tracking-wider",
                    "shadow-[inset_0_0_0_0.125em_rgba(0,0,0,1)]",
                    "flex items-center justify-center px-4 py-2",
                    "origin-[1.25em_50%]",
                    coverClassName
                )}
                animate={{
                    rotate: isHovered ? -12 : 0,
                }}
                transition={{
                    type: "spring",
                    stiffness: 300,
                    damping: 20,
                    mass: 0.8,
                }}
            >
                {children}
            </motion.span>

            {/* Invisible placeholder to maintain size since cover is absolute */}
            <span className="block opacity-0 px-4 py-2 font-bold tracking-wider min-w-[9em]">
                {children}
            </span>
        </button>
    );
};

export default CreepyButton;

```

---

## `src\components\ui\cursor-card.tsx`

```tsx
"use client";

import React, { useState, useEffect } from "react";
import { createPortal } from "react-dom";
import { motion, useMotionValue, useSpring, AnimatePresence } from "framer-motion";
import { cn } from "@/lib/utils";

export interface CursorCardProps {
  children: React.ReactNode;
  image: string;
  description: string;
  href?: string;
  className?: string;
}

export function CursorCard({ children, image, description, href = "#", className }: CursorCardProps) {
  const [isHovered, setIsHovered] = useState(false);
  const [mounted, setMounted] = useState(false);

  const x = useMotionValue(0);
  const y = useMotionValue(0);

  const springConfig = { damping: 25, stiffness: 300 };
  const springX = useSpring(x, springConfig);
  const springY = useSpring(y, springConfig);

  useEffect(() => {
    setMounted(true);
  }, []);

  const handleMouseMove = (e: React.MouseEvent) => {
    x.set(e.clientX - 120); // Center horizontally (width 240 / 2)
    y.set(e.clientY + 20);  // Offset vertically slightly below cursor
  };

  return (
    <>
      <a
        href={href}
        className={cn(
          "relative inline-block font-bold text-neutral-900 dark:text-neutral-100 transition-colors",
          "hover:bg-orange-100 dark:hover:bg-orange-900/40 rounded px-1 -mx-1",
          className
        )}
        onMouseEnter={() => setIsHovered(true)}
        onMouseLeave={() => setIsHovered(false)}
        onMouseMove={handleMouseMove}
      >
        {children}
      </a>

      {mounted && typeof document !== "undefined" && createPortal(
        <AnimatePresence>
          {isHovered && (
            <motion.div
              initial={{ opacity: 0, scale: 0.8 }}
              animate={{ opacity: 1, scale: 1 }}
              exit={{ opacity: 0, scale: 0.8 }}
              transition={{ duration: 0.15, ease: "easeOut" }}
              style={{
                x: springX,
                y: springY,
              }}
              className={cn(
                "fixed top-0 left-0 pointer-events-none z-50 w-[240px]",
                "bg-white dark:bg-neutral-900 p-3 shadow-2xl rounded-xl border border-neutral-200 dark:border-neutral-800"
              )}
            >
              {/* eslint-disable-next-line @next/next/no-img-element */}
              <img src={image} alt="hover preview" className="w-full h-auto rounded-md mb-3 object-cover" />
              <p className="text-sm text-neutral-600 dark:text-neutral-400 m-0 leading-relaxed">
                {description}
              </p>
            </motion.div>
          )}
        </AnimatePresence>,
        document.body
      )}
    </>
  );
}

export default CursorCard;

```

---

## `src\components\ui\cyber-glitch-text.tsx`

```tsx
"use client";

import React, { useEffect, useRef, useState } from "react";
import { cn } from "@/lib/utils";
import { motion } from "framer-motion";

const CHARS = "!<>-_\\/[]{}—=+*^?#_";

interface CyberGlitchTextProps {
  text: string;
  className?: string;
  scrambleOnMount?: boolean;
  scrambleDuration?: number;
}

export function CyberGlitchText({
  text,
  className,
  scrambleOnMount = true,
  scrambleDuration = 40,
}: CyberGlitchTextProps) {
  const [displayText, setDisplayText] = useState(text);
  const [isHovered, setIsHovered] = useState(false);
  const intervalRef = useRef<NodeJS.Timeout | null>(null);

  const scramble = () => {
    let iteration = 0;
    clearInterval(intervalRef.current as NodeJS.Timeout);

    intervalRef.current = setInterval(() => {
      setDisplayText((currentText) =>
        text
          .split("")
          .map((letter, index) => {
            if (index < iteration) {
              return text[index];
            }
            return CHARS[Math.floor(Math.random() * CHARS.length)];
          })
          .join("")
      );

      if (iteration >= text.length) {
        clearInterval(intervalRef.current as NodeJS.Timeout);
      }

      iteration += 1 / 3;
    }, scrambleDuration);
  };

  useEffect(() => {
    if (scrambleOnMount) {
      scramble();
    }
    return () => clearInterval(intervalRef.current as NodeJS.Timeout);
  }, [text, scrambleOnMount]);

  return (
    <div
      className={cn("relative inline-block group", className)}
      onMouseEnter={() => {
        setIsHovered(true);
        scramble();
      }}
      onMouseLeave={() => setIsHovered(false)}
    >
      {/* Base Text */}
      <span className="relative z-10">{displayText}</span>

      {/* Glitch Layers for Chromatic Aberration */}
      {isHovered && (
        <>
          <motion.span
            className="absolute top-0 left-[-2px] z-0 text-red-500 opacity-70 mix-blend-screen"
            initial={{ x: 0, opacity: 0 }}
            animate={{ x: [-2, 2, -1, 3, 0], opacity: [0, 0.8, 0.4, 0.9, 0] }}
            transition={{ duration: 0.2, repeat: Infinity, repeatType: "mirror" }}
            aria-hidden="true"
          >
            {displayText}
          </motion.span>
          <motion.span
            className="absolute top-0 left-[2px] z-0 text-blue-500 opacity-70 mix-blend-screen"
            initial={{ x: 0, opacity: 0 }}
            animate={{ x: [2, -2, 1, -3, 0], opacity: [0, 0.8, 0.4, 0.9, 0] }}
            transition={{ duration: 0.2, repeat: Infinity, repeatType: "mirror", delay: 0.05 }}
            aria-hidden="true"
          >
            {displayText}
          </motion.span>
          
          {/* Slice Glitch Overlay */}
          <motion.div
            className="absolute inset-0 bg-white/10 dark:bg-black/10 z-20 pointer-events-none mix-blend-overlay"
            initial={{ top: "0%", height: "0%" }}
            animate={{ top: ["0%", "40%", "80%", "0%"], height: ["2px", "5px", "1px", "0px"] }}
            transition={{ duration: 0.3, repeat: Infinity }}
          />
        </>
      )}
    </div>
  );
}

```

---

## `src\components\ui\cylinder-carousel.tsx`

```tsx
"use client";

import React, { useMemo } from "react";
import { cn } from "@/lib/utils";

export interface CarouselImage {
  src: string;
  alt?: string;
}

export interface CylinderCarouselProps extends React.HTMLAttributes<HTMLDivElement> {
  images: CarouselImage[];
  containerClassName?: string;
  cardClassName?: string;
  animationDuration?: number; // in seconds
  cardWidth?: number; // in pixels
}

export const CylinderCarousel = React.forwardRef<HTMLDivElement, CylinderCarouselProps>(
  (
    {
      images,
      className,
      containerClassName,
      cardClassName,
      animationDuration = 32,
      cardWidth = 250,
      ...props
    },
    ref
  ) => {
    const N = images.length;
    
    // We compute the CSS variables here instead of polluting the global CSS
    // --n: number of cards
    // --w: card width
    const customStyle = {
      "--n": N,
      "--w": `${cardWidth}px`,
      "--ba": `calc(1turn / var(--n))`,
      // animation duration
      "--anim-dur": `${animationDuration}s`,
    } as React.CSSProperties;

    return (
      <div
        ref={ref}
        className={cn(
          "w-full h-full min-h-[500px] grid place-items-center overflow-hidden",
          className
        )}
        style={{
          perspective: "35em",
          maskImage: "linear-gradient(90deg, transparent, #000 20% 80%, transparent)",
          WebkitMaskImage: "linear-gradient(90deg, transparent, #000 20% 80%, transparent)",
        }}
        {...props}
      >
        <div
          className={cn(
            "grid place-items-center [transform-style:preserve-3d] motion-reduce:!animate-[ry_128s_linear_infinite]",
            containerClassName
          )}
          style={{
            ...customStyle,
            animation: "ry var(--anim-dur) linear infinite",
          }}
        >
          {/* We define the keyframes inline via a style block to ensure it works without global CSS config */}
          <style>
            {`
              @keyframes ry {
                to { transform: rotateY(1turn); }
              }
            `}
          </style>
          
          {images.map((img, i) => (
            <img
              key={i}
              src={img.src}
              alt={img.alt || `Carousel image ${i}`}
              className={cn(
                "[grid-area:1/1] object-cover rounded-2xl [backface-visibility:hidden]",
                cardClassName
              )}
              style={{
                width: "var(--w)",
                aspectRatio: "7/10",
                "--i": i,
                // transform: rotateY(calc(var(--i) * var(--ba))) translateZ(calc(-1 * (0.5 * var(--w) + 0.5em) / tan(0.5 * var(--ba))))
                // Note: using modern CSS tan() function. Fallback translates are recommended if targeting very old browsers.
                transform: "rotateY(calc(var(--i) * var(--ba))) translateZ(calc(-1 * (0.5 * var(--w) + 0.5em) / tan(0.5 * var(--ba))))",
              } as React.CSSProperties}
            />
          ))}
        </div>
      </div>
    );
  }
);

CylinderCarousel.displayName = "CylinderCarousel";

```

---

## `src\components\ui\diagonal-carousel.tsx`

```tsx
"use client";

import * as React from "react";
import { motion, type Transition } from "framer-motion";
import { ChevronLeft, ChevronRight } from "lucide-react";
import { cn } from "@/lib/utils";

export interface DiagonalCarouselItem {
  src: string;
  title: string;
  alt?: string;
}

export interface DiagonalCarouselProps
  extends Omit<React.HTMLAttributes<HTMLDivElement>, "onChange"> {
  items: DiagonalCarouselItem[];
  activeIndex?: number;
  defaultActiveIndex?: number;
  onActiveIndexChange?: (index: number) => void;
  loop?: boolean;
  slideSize?: number;
  rotationStep?: number;
  verticalStep?: number;
  inactiveScale?: number;
  transition?: Transition;
  showControls?: boolean;
  showDots?: boolean;
  viewportClassName?: string;
  slideClassName?: string;
  imageClassName?: string;
  labelClassName?: string;
  controlsClassName?: string;
}

const DEFAULT_TRANSITION: Transition = {
  type: "spring",
  bounce: 0.16,
  duration: 0.85,
};

const clamp = (value: number, min: number, max: number) => Math.min(Math.max(value, min), max);

export function DiagonalCarousel({
  items,
  activeIndex,
  defaultActiveIndex = 0,
  onActiveIndexChange,
  loop = false,
  slideSize = 260,
  rotationStep = 30,
  verticalStep = 120,
  inactiveScale = 0.6,
  transition = DEFAULT_TRANSITION,
  showControls = true,
  showDots = true,
  viewportClassName,
  slideClassName,
  imageClassName,
  labelClassName,
  controlsClassName,
  className,
  onKeyDown,
  tabIndex,
  ...props
}: DiagonalCarouselProps) {
  const maxIndex = Math.max(0, items.length - 1);
  const [uncontrolledIndex, setUncontrolledIndex] = React.useState(() =>
    clamp(defaultActiveIndex, 0, maxIndex)
  );
  const currentIndex = clamp(activeIndex ?? uncontrolledIndex, 0, maxIndex);
  const safeSlideSize = Math.max(120, slideSize);
  const safeInactiveScale = clamp(inactiveScale, 0.35, 1);

  const selectSlide = React.useCallback(
    (nextIndex: number) => {
      if (!items.length) {
        return;
      }

      const resolvedIndex = loop
        ? (nextIndex + items.length) % items.length
        : clamp(nextIndex, 0, maxIndex);

      if (activeIndex === undefined) {
        setUncontrolledIndex(resolvedIndex);
      }

      onActiveIndexChange?.(resolvedIndex);
    },
    [activeIndex, items.length, loop, maxIndex, onActiveIndexChange]
  );

  const handleKeyDown = (event: React.KeyboardEvent<HTMLDivElement>) => {
    onKeyDown?.(event);

    if (event.defaultPrevented) {
      return;
    }

    if (event.key === "ArrowLeft") {
      event.preventDefault();
      selectSlide(currentIndex - 1);
    }

    if (event.key === "ArrowRight") {
      event.preventDefault();
      selectSlide(currentIndex + 1);
    }
  };

  if (!items.length) {
    return null;
  }

  const isPreviousDisabled = !loop && currentIndex === 0;
  const isNextDisabled = !loop && currentIndex === maxIndex;

  return (
    <div
      role="region"
      aria-roledescription="carousel"
      aria-label="Diagonal image carousel"
      tabIndex={tabIndex ?? 0}
      onKeyDown={handleKeyDown}
      className={cn("relative isolate h-full w-full overflow-hidden", className)}
      {...props}
    >
      <div className={cn("absolute inset-0 overflow-hidden", viewportClassName)}>
        <motion.div
          className="absolute left-1/2 top-[30%] flex w-fit"
          animate={{ x: -(currentIndex * safeSlideSize + safeSlideSize / 2) }}
          transition={transition}
        >
          {items.map((item, index) => {
            const isActive = currentIndex === index;
            const distance = index - currentIndex;

            return (
              <motion.div
                key={`${item.src}-${index}`}
                className={cn(
                  "flex shrink-0 flex-col items-center gap-2 will-change-transform",
                  slideClassName
                )}
                style={{ width: safeSlideSize }}
                animate={{
                  rotate: distance * rotationStep,
                  scale: isActive ? 1 : safeInactiveScale,
                  y: distance * verticalStep,
                }}
                transition={transition}
              >
                <motion.p
                  className={cn("whitespace-nowrap text-sm", labelClassName)}
                  animate={{
                    opacity: isActive ? 1 : 0,
                    scale: isActive ? 1 : 0.7,
                  }}
                  transition={{ duration: 0.3 }}
                >
                  {item.title}
                </motion.p>

                <button
                  type="button"
                  aria-label={`Show ${item.title}`}
                  aria-current={isActive ? "true" : undefined}
                  className="aspect-square w-full cursor-pointer"
                  onClick={() => selectSlide(index)}
                >
                  {/* eslint-disable-next-line @next/next/no-img-element */}
                  <img
                    src={item.src}
                    alt={item.alt ?? item.title}
                    draggable={false}
                    className={cn(
                      "h-full w-full select-none rounded-2xl object-cover shadow-xl",
                      imageClassName
                    )}
                  />
                </button>
              </motion.div>
            );
          })}
        </motion.div>
      </div>

      {showControls && (
        <div
          className={cn(
            "absolute inset-x-4 bottom-5 z-10 mx-auto flex w-fit items-center justify-center gap-3 rounded-full border border-neutral-300/80 bg-neutral-200/70 px-2 text-neutral-700 shadow-sm backdrop-blur-sm dark:border-white/10 dark:bg-neutral-900/70 dark:text-neutral-100",
            controlsClassName
          )}
        >
          <button
            type="button"
            aria-label="Show previous slide"
            disabled={isPreviousDisabled}
            className="inline-flex size-9 items-center justify-center rounded-full transition-colors hover:bg-white/70 disabled:cursor-not-allowed disabled:opacity-35 dark:hover:bg-white/10"
            onClick={() => selectSlide(currentIndex - 1)}
          >
            <ChevronLeft className="size-5" />
          </button>

          {showDots && (
            <div className="flex items-center justify-center gap-2">
              {items.map((item, index) => (
                <button
                  key={`${item.title}-${index}`}
                  type="button"
                  aria-label={`Show slide ${index + 1}: ${item.title}`}
                  aria-current={currentIndex === index ? "true" : undefined}
                  className={cn(
                    "h-2 rounded-full bg-current transition-[width,opacity] duration-300",
                    currentIndex === index ? "w-7 opacity-100" : "w-2 opacity-30"
                  )}
                  onClick={() => selectSlide(index)}
                />
              ))}
            </div>
          )}

          <button
            type="button"
            aria-label="Show next slide"
            disabled={isNextDisabled}
            className="inline-flex size-9 items-center justify-center rounded-full transition-colors hover:bg-white/70 disabled:cursor-not-allowed disabled:opacity-35 dark:hover:bg-white/10"
            onClick={() => selectSlide(currentIndex + 1)}
          >
            <ChevronRight className="size-5" />
          </button>
        </div>
      )}
    </div>
  );
}

export default DiagonalCarousel;

```

---

## `src\components\ui\dialog.tsx`

```tsx
'use client'

import * as React from 'react'
import * as DialogPrimitive from '@radix-ui/react-dialog'

import { cn } from '@/lib/utils'

function Dialog({ ...props }: React.ComponentProps<typeof DialogPrimitive.Root>) {
    return (
        <DialogPrimitive.Root
            data-slot="dialog"
            {...props}
        />
    )
}

function DialogTrigger({ ...props }: React.ComponentProps<typeof DialogPrimitive.Trigger>) {
    return (
        <DialogPrimitive.Trigger
            data-slot="dialog-trigger"
            {...props}
        />
    )
}

function DialogPortal({ ...props }: React.ComponentProps<typeof DialogPrimitive.Portal>) {
    return (
        <DialogPrimitive.Portal
            data-slot="dialog-portal"
            {...props}
        />
    )
}

function DialogClose({ ...props }: React.ComponentProps<typeof DialogPrimitive.Close>) {
    return (
        <DialogPrimitive.Close
            data-slot="dialog-close"
            {...props}
        />
    )
}

function DialogOverlay({ className, ...props }: React.ComponentProps<typeof DialogPrimitive.Overlay>) {
    return (
        <DialogPrimitive.Overlay
            data-slot="dialog-overlay"
            className={cn('data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0 fixed inset-0 z-[1000] bg-black/80', className)}
            {...props}
        />
    )
}

function DialogContent({ className, children, ...props }: React.ComponentProps<typeof DialogPrimitive.Content>) {
    return (
        <DialogPortal data-slot="dialog-portal">
            <DialogOverlay />
            <DialogPrimitive.Content
                data-slot="dialog-content"
                className={cn('bg-background data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0 data-[state=closed]:zoom-out-95 data-[state=open]:zoom-in-95 fixed left-[50%] top-[50%] z-[1001] grid w-full max-w-[calc(100%-2rem)] translate-x-[-50%] translate-y-[-50%] gap-4 rounded-lg border p-6 shadow-lg duration-200 sm:max-w-lg', className)}
                {...props}>
                {children}
            </DialogPrimitive.Content>
        </DialogPortal>
    )
}

function DialogHeader({ className, ...props }: React.ComponentProps<'div'>) {
    return (
        <div
            data-slot="dialog-header"
            className={cn('flex flex-col gap-2 text-center sm:text-left', className)}
            {...props}
        />
    )
}

function DialogFooter({ className, ...props }: React.ComponentProps<'div'>) {
    return (
        <div
            data-slot="dialog-footer"
            className={cn('flex flex-col-reverse gap-2 sm:flex-row sm:justify-end', className)}
            {...props}
        />
    )
}

function DialogTitle({ className, ...props }: React.ComponentProps<typeof DialogPrimitive.Title>) {
    return (
        <DialogPrimitive.Title
            data-slot="dialog-title"
            className={cn('text-lg font-semibold leading-none', className)}
            {...props}
        />
    )
}

function DialogDescription({ className, ...props }: React.ComponentProps<typeof DialogPrimitive.Description>) {
    return (
        <DialogPrimitive.Description
            data-slot="dialog-description"
            className={cn('text-muted-foreground text-sm', className)}
            {...props}
        />
    )
}

export { Dialog, DialogClose, DialogContent, DialogDescription, DialogFooter, DialogHeader, DialogOverlay, DialogPortal, DialogTitle, DialogTrigger }

```

---

## `src\components\ui\drawer.tsx`

```tsx
"use client"

import * as React from "react"
import { Drawer as DrawerPrimitive } from "vaul"

import { cn } from "@/lib/utils"

function Drawer({
  ...props
}: React.ComponentProps<typeof DrawerPrimitive.Root>) {
  return <DrawerPrimitive.Root data-slot="drawer" {...props} />
}

function DrawerTrigger({
  ...props
}: React.ComponentProps<typeof DrawerPrimitive.Trigger>) {
  return <DrawerPrimitive.Trigger data-slot="drawer-trigger" {...props} />
}

function DrawerPortal({
  ...props
}: React.ComponentProps<typeof DrawerPrimitive.Portal>) {
  return <DrawerPrimitive.Portal data-slot="drawer-portal" {...props} />
}

function DrawerClose({
  ...props
}: React.ComponentProps<typeof DrawerPrimitive.Close>) {
  return <DrawerPrimitive.Close data-slot="drawer-close" {...props} />
}

function DrawerOverlay({
  className,
  ...props
}: React.ComponentProps<typeof DrawerPrimitive.Overlay>) {
  return (
    <DrawerPrimitive.Overlay
      data-slot="drawer-overlay"
      className={cn(
        "data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0 fixed inset-0 z-50 bg-black/50",
        className
      )}
      {...props}
    />
  )
}

function DrawerContent({
  className,
  children,
  ...props
}: React.ComponentProps<typeof DrawerPrimitive.Content>) {
  return (
    <DrawerPortal data-slot="drawer-portal">
      <DrawerOverlay />
      <DrawerPrimitive.Content
        data-slot="drawer-content"
        className={cn(
          "group/drawer-content bg-background fixed z-50 flex h-auto flex-col",
          "data-[vaul-drawer-direction=top]:inset-x-0 data-[vaul-drawer-direction=top]:top-0 data-[vaul-drawer-direction=top]:mb-24 data-[vaul-drawer-direction=top]:max-h-[80vh] data-[vaul-drawer-direction=top]:rounded-b-lg data-[vaul-drawer-direction=top]:border-b",
          "data-[vaul-drawer-direction=bottom]:inset-x-0 data-[vaul-drawer-direction=bottom]:bottom-0 data-[vaul-drawer-direction=bottom]:mt-24 data-[vaul-drawer-direction=bottom]:max-h-[80vh] data-[vaul-drawer-direction=bottom]:rounded-t-lg data-[vaul-drawer-direction=bottom]:border-t",
          "data-[vaul-drawer-direction=right]:inset-y-0 data-[vaul-drawer-direction=right]:right-0 data-[vaul-drawer-direction=right]:w-3/4 data-[vaul-drawer-direction=right]:border-l data-[vaul-drawer-direction=right]:sm:max-w-sm",
          "data-[vaul-drawer-direction=left]:inset-y-0 data-[vaul-drawer-direction=left]:left-0 data-[vaul-drawer-direction=left]:w-3/4 data-[vaul-drawer-direction=left]:border-r data-[vaul-drawer-direction=left]:sm:max-w-sm",
          className
        )}
        {...props}
      >
        <div className="bg-muted mx-auto mt-4 hidden h-2 w-[100px] shrink-0 rounded-full group-data-[vaul-drawer-direction=bottom]/drawer-content:block" />
        {children}
      </DrawerPrimitive.Content>
    </DrawerPortal>
  )
}

function DrawerHeader({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="drawer-header"
      className={cn(
        "flex flex-col gap-0.5 p-4 group-data-[vaul-drawer-direction=bottom]/drawer-content:text-center group-data-[vaul-drawer-direction=top]/drawer-content:text-center md:gap-1.5 md:text-left",
        className
      )}
      {...props}
    />
  )
}

function DrawerFooter({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="drawer-footer"
      className={cn("mt-auto flex flex-col gap-2 p-4", className)}
      {...props}
    />
  )
}

function DrawerTitle({
  className,
  ...props
}: React.ComponentProps<typeof DrawerPrimitive.Title>) {
  return (
    <DrawerPrimitive.Title
      data-slot="drawer-title"
      className={cn("text-foreground font-semibold", className)}
      {...props}
    />
  )
}

function DrawerDescription({
  className,
  ...props
}: React.ComponentProps<typeof DrawerPrimitive.Description>) {
  return (
    <DrawerPrimitive.Description
      data-slot="drawer-description"
      className={cn("text-muted-foreground text-sm", className)}
      {...props}
    />
  )
}

export {
  Drawer,
  DrawerPortal,
  DrawerOverlay,
  DrawerTrigger,
  DrawerClose,
  DrawerContent,
  DrawerHeader,
  DrawerFooter,
  DrawerTitle,
  DrawerDescription,
}

```

---

## `src\components\ui\dropdown-menu.tsx`

```tsx
"use client"

import * as React from "react"
import * as DropdownMenuPrimitive from "@radix-ui/react-dropdown-menu"
import { CheckIcon, ChevronRightIcon, CircleIcon } from "lucide-react"

import { cn } from "@/lib/utils"

function DropdownMenu({
  ...props
}: React.ComponentProps<typeof DropdownMenuPrimitive.Root>) {
  return <DropdownMenuPrimitive.Root data-slot="dropdown-menu" {...props} />
}

function DropdownMenuPortal({
  ...props
}: React.ComponentProps<typeof DropdownMenuPrimitive.Portal>) {
  return (
    <DropdownMenuPrimitive.Portal data-slot="dropdown-menu-portal" {...props} />
  )
}

function DropdownMenuTrigger({
  ...props
}: React.ComponentProps<typeof DropdownMenuPrimitive.Trigger>) {
  return (
    <DropdownMenuPrimitive.Trigger
      data-slot="dropdown-menu-trigger"
      {...props}
    />
  )
}

function DropdownMenuContent({
  className,
  sideOffset = 4,
  ...props
}: React.ComponentProps<typeof DropdownMenuPrimitive.Content>) {
  return (
    <DropdownMenuPrimitive.Portal>
      <DropdownMenuPrimitive.Content
        data-slot="dropdown-menu-content"
        sideOffset={sideOffset}
        className={cn(
          "bg-popover text-popover-foreground data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0 data-[state=closed]:zoom-out-95 data-[state=open]:zoom-in-95 data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-from-right-2 data-[side=right]:slide-in-from-left-2 data-[side=top]:slide-in-from-bottom-2 z-50 max-h-(--radix-dropdown-menu-content-available-height) min-w-[8rem] origin-(--radix-dropdown-menu-content-transform-origin) overflow-x-hidden overflow-y-auto rounded-md border p-1 shadow-md",
          className
        )}
        {...props}
      />
    </DropdownMenuPrimitive.Portal>
  )
}

function DropdownMenuGroup({
  ...props
}: React.ComponentProps<typeof DropdownMenuPrimitive.Group>) {
  return (
    <DropdownMenuPrimitive.Group data-slot="dropdown-menu-group" {...props} />
  )
}

function DropdownMenuItem({
  className,
  inset,
  variant = "default",
  ...props
}: React.ComponentProps<typeof DropdownMenuPrimitive.Item> & {
  inset?: boolean
  variant?: "default" | "destructive"
}) {
  return (
    <DropdownMenuPrimitive.Item
      data-slot="dropdown-menu-item"
      data-inset={inset}
      data-variant={variant}
      className={cn(
        "focus:bg-accent focus:text-accent-foreground data-[variant=destructive]:text-destructive data-[variant=destructive]:focus:bg-destructive/10 dark:data-[variant=destructive]:focus:bg-destructive/20 data-[variant=destructive]:focus:text-destructive data-[variant=destructive]:*:[svg]:!text-destructive [&_svg:not([class*='text-'])]:text-muted-foreground relative flex cursor-default items-center gap-2 rounded-sm px-2 py-1.5 text-sm outline-hidden select-none data-[disabled]:pointer-events-none data-[disabled]:opacity-50 data-[inset]:pl-8 [&_svg]:pointer-events-none [&_svg]:shrink-0 [&_svg:not([class*='size-'])]:size-4",
        className
      )}
      {...props}
    />
  )
}

function DropdownMenuCheckboxItem({
  className,
  children,
  checked,
  ...props
}: React.ComponentProps<typeof DropdownMenuPrimitive.CheckboxItem>) {
  return (
    <DropdownMenuPrimitive.CheckboxItem
      data-slot="dropdown-menu-checkbox-item"
      className={cn(
        "focus:bg-accent focus:text-accent-foreground relative flex cursor-default items-center gap-2 rounded-sm py-1.5 pr-2 pl-8 text-sm outline-hidden select-none data-[disabled]:pointer-events-none data-[disabled]:opacity-50 [&_svg]:pointer-events-none [&_svg]:shrink-0 [&_svg:not([class*='size-'])]:size-4",
        className
      )}
      checked={checked}
      {...props}
    >
      <span className="pointer-events-none absolute left-2 flex size-3.5 items-center justify-center">
        <DropdownMenuPrimitive.ItemIndicator>
          <CheckIcon className="size-4" />
        </DropdownMenuPrimitive.ItemIndicator>
      </span>
      {children}
    </DropdownMenuPrimitive.CheckboxItem>
  )
}

function DropdownMenuRadioGroup({
  ...props
}: React.ComponentProps<typeof DropdownMenuPrimitive.RadioGroup>) {
  return (
    <DropdownMenuPrimitive.RadioGroup
      data-slot="dropdown-menu-radio-group"
      {...props}
    />
  )
}

function DropdownMenuRadioItem({
  className,
  children,
  ...props
}: React.ComponentProps<typeof DropdownMenuPrimitive.RadioItem>) {
  return (
    <DropdownMenuPrimitive.RadioItem
      data-slot="dropdown-menu-radio-item"
      className={cn(
        "focus:bg-accent focus:text-accent-foreground relative flex cursor-default items-center gap-2 rounded-sm py-1.5 pr-2 pl-8 text-sm outline-hidden select-none data-[disabled]:pointer-events-none data-[disabled]:opacity-50 [&_svg]:pointer-events-none [&_svg]:shrink-0 [&_svg:not([class*='size-'])]:size-4",
        className
      )}
      {...props}
    >
      <span className="pointer-events-none absolute left-2 flex size-3.5 items-center justify-center">
        <DropdownMenuPrimitive.ItemIndicator>
          <CircleIcon className="size-2 fill-current" />
        </DropdownMenuPrimitive.ItemIndicator>
      </span>
      {children}
    </DropdownMenuPrimitive.RadioItem>
  )
}

function DropdownMenuLabel({
  className,
  inset,
  ...props
}: React.ComponentProps<typeof DropdownMenuPrimitive.Label> & {
  inset?: boolean
}) {
  return (
    <DropdownMenuPrimitive.Label
      data-slot="dropdown-menu-label"
      data-inset={inset}
      className={cn(
        "px-2 py-1.5 text-sm font-medium data-[inset]:pl-8",
        className
      )}
      {...props}
    />
  )
}

function DropdownMenuSeparator({
  className,
  ...props
}: React.ComponentProps<typeof DropdownMenuPrimitive.Separator>) {
  return (
    <DropdownMenuPrimitive.Separator
      data-slot="dropdown-menu-separator"
      className={cn("bg-border -mx-1 my-1 h-px", className)}
      {...props}
    />
  )
}

function DropdownMenuShortcut({
  className,
  ...props
}: React.ComponentProps<"span">) {
  return (
    <span
      data-slot="dropdown-menu-shortcut"
      className={cn(
        "text-muted-foreground ml-auto text-xs tracking-widest",
        className
      )}
      {...props}
    />
  )
}

function DropdownMenuSub({
  ...props
}: React.ComponentProps<typeof DropdownMenuPrimitive.Sub>) {
  return <DropdownMenuPrimitive.Sub data-slot="dropdown-menu-sub" {...props} />
}

function DropdownMenuSubTrigger({
  className,
  inset,
  children,
  ...props
}: React.ComponentProps<typeof DropdownMenuPrimitive.SubTrigger> & {
  inset?: boolean
}) {
  return (
    <DropdownMenuPrimitive.SubTrigger
      data-slot="dropdown-menu-sub-trigger"
      data-inset={inset}
      className={cn(
        "focus:bg-accent focus:text-accent-foreground data-[state=open]:bg-accent data-[state=open]:text-accent-foreground [&_svg:not([class*='text-'])]:text-muted-foreground flex cursor-default items-center gap-2 rounded-sm px-2 py-1.5 text-sm outline-hidden select-none data-[inset]:pl-8 [&_svg]:pointer-events-none [&_svg]:shrink-0 [&_svg:not([class*='size-'])]:size-4",
        className
      )}
      {...props}
    >
      {children}
      <ChevronRightIcon className="ml-auto size-4" />
    </DropdownMenuPrimitive.SubTrigger>
  )
}

function DropdownMenuSubContent({
  className,
  ...props
}: React.ComponentProps<typeof DropdownMenuPrimitive.SubContent>) {
  return (
    <DropdownMenuPrimitive.SubContent
      data-slot="dropdown-menu-sub-content"
      className={cn(
        "bg-popover text-popover-foreground data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0 data-[state=closed]:zoom-out-95 data-[state=open]:zoom-in-95 data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-from-right-2 data-[side=right]:slide-in-from-left-2 data-[side=top]:slide-in-from-bottom-2 z-50 min-w-[8rem] origin-(--radix-dropdown-menu-content-transform-origin) overflow-hidden rounded-md border p-1 shadow-lg",
        className
      )}
      {...props}
    />
  )
}

export {
  DropdownMenu,
  DropdownMenuPortal,
  DropdownMenuTrigger,
  DropdownMenuContent,
  DropdownMenuGroup,
  DropdownMenuLabel,
  DropdownMenuItem,
  DropdownMenuCheckboxItem,
  DropdownMenuRadioGroup,
  DropdownMenuRadioItem,
  DropdownMenuSeparator,
  DropdownMenuShortcut,
  DropdownMenuSub,
  DropdownMenuSubTrigger,
  DropdownMenuSubContent,
}

```

---

## `src\components\ui\elastic-stack.tsx`

```tsx
"use client";

import React, { useState } from "react";
import { cn } from "@/lib/utils";

export interface ElasticStackItem {
  id: string | number;
  image?: string;
  name?: string;
}

export interface ElasticStackProps extends React.HTMLAttributes<HTMLDivElement> {
  items: ElasticStackItem[];
  itemSize?: number;
  overlap?: number;
  pushForce?: number;
}

export function ElasticStack({
  items,
  itemSize = 70,
  overlap = 30,
  pushForce = 15,
  className,
  ...props
}: ElasticStackProps) {
  const [hoveredIndex, setHoveredIndex] = useState<number | null>(null);

  const total = items.length;
  // Custom spring-like easing from the original CSS
  const springEasing = "linear(0, 0.79 14.4%, 1.026 22.4%, 1.164 31.2%, 1.207 38.2%, 1.208 46.2%, 1.033 80%, 1)";

  return (
    <div
      className={cn("flex items-center justify-center cursor-pointer py-8", className)}
      onMouseLeave={() => setHoveredIndex(null)}
      {...props}
    >
      {items.map((item, i) => {
        let translateX = 0;
        let scale = 1;
        let zIndex = i; // Base stacking order
        let isHovered = hoveredIndex === i;

        if (hoveredIndex !== null) {
          if (i > hoveredIndex) {
            translateX = Math.min(pushForce * (total - i - 1), overlap);
          } else if (i < hoveredIndex) {
            translateX = -Math.min(pushForce * i, overlap);
          } else {
            scale = 1.25;
            zIndex = 100;
          }
        }

        return (
          <div
            key={item.id}
            onMouseEnter={() => setHoveredIndex(i)}
            className={cn(
              "relative flex items-center justify-center rounded-full isolate transition-all duration-700 bg-neutral-100 dark:bg-neutral-800",
              "border-2 border-white dark:border-neutral-950",
              isHovered ? "shadow-xl" : "shadow-sm"
            )}
            style={{
              width: itemSize,
              height: itemSize,
              marginLeft: i === 0 ? 0 : -overlap,
              transform: `translateX(${translateX}px) scale(${scale})`,
              transitionTimingFunction: springEasing,
              zIndex,
            }}
          >
            {item.image ? (
              // eslint-disable-next-line @next/next/no-img-element
              <img 
                src={item.image} 
                alt={item.name || `Avatar ${i}`}
                className="w-full h-full object-cover rounded-full pointer-events-none"
              />
            ) : (
              <div className="w-full h-full rounded-full flex items-center justify-center font-semibold text-neutral-500 dark:text-neutral-400">
                {item.name ? item.name.charAt(0) : i + 1}
              </div>
            )}
          </div>
        );
      })}
    </div>
  );
}

export default ElasticStack;

```

---

## `src\components\ui\empty.tsx`

```tsx
import { cva, type VariantProps } from "class-variance-authority"

import { cn } from "@/lib/utils"

function Empty({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="empty"
      className={cn(
        "flex min-w-0 flex-1 flex-col items-center justify-center gap-6 rounded-lg border-dashed p-6 text-center text-balance md:p-12",
        className
      )}
      {...props}
    />
  )
}

function EmptyHeader({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="empty-header"
      className={cn(
        "flex max-w-sm flex-col items-center gap-2 text-center",
        className
      )}
      {...props}
    />
  )
}

const emptyMediaVariants = cva(
  "flex shrink-0 items-center justify-center mb-2 [&_svg]:pointer-events-none [&_svg]:shrink-0",
  {
    variants: {
      variant: {
        default: "bg-transparent",
        icon: "bg-muted text-foreground flex size-10 shrink-0 items-center justify-center rounded-lg [&_svg:not([class*='size-'])]:size-6",
      },
    },
    defaultVariants: {
      variant: "default",
    },
  }
)

function EmptyMedia({
  className,
  variant = "default",
  ...props
}: React.ComponentProps<"div"> & VariantProps<typeof emptyMediaVariants>) {
  return (
    <div
      data-slot="empty-icon"
      data-variant={variant}
      className={cn(emptyMediaVariants({ variant, className }))}
      {...props}
    />
  )
}

function EmptyTitle({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="empty-title"
      className={cn("text-lg font-medium tracking-tight", className)}
      {...props}
    />
  )
}

function EmptyDescription({ className, ...props }: React.ComponentProps<"p">) {
  return (
    <div
      data-slot="empty-description"
      className={cn(
        "text-muted-foreground [&>a:hover]:text-primary text-sm/relaxed [&>a]:underline [&>a]:underline-offset-4",
        className
      )}
      {...props}
    />
  )
}

function EmptyContent({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="empty-content"
      className={cn(
        "flex w-full max-w-sm min-w-0 flex-col items-center gap-4 text-sm text-balance",
        className
      )}
      {...props}
    />
  )
}

export {
  Empty,
  EmptyHeader,
  EmptyTitle,
  EmptyDescription,
  EmptyContent,
  EmptyMedia,
}

```

---

## `src\components\ui\expandable-bento-grid.tsx`

```tsx
'use client'

import React, { useEffect, useId, useRef, useState } from 'react'
import { AnimatePresence, motion } from 'framer-motion'
import { useOutsideClick } from '@/hooks/use-outside-click'
import { X } from 'lucide-react'

export interface BentoGridProps {
    items: {
        id: string | number
        title: string
        subtitle?: string
        description?: string
        content: React.ReactNode
        icon?: React.ReactNode
        className?: string
    }[]
}

export default function ExpandableBentoGrid({ items }: BentoGridProps) {
    const [active, setActive] = useState<(typeof items)[number] | boolean | null>(null)
    const ref = useRef<HTMLDivElement>(null)
    const id = useId()

    useEffect(() => {
        function onKeyDown(event: KeyboardEvent) {
            if (event.key === 'Escape') {
                setActive(false)
            }
        }

        if (active && typeof active === 'object') {
            document.body.style.overflow = 'hidden'
        } else {
            document.body.style.overflow = 'auto'
        }

        window.addEventListener('keydown', onKeyDown)
        return () => window.removeEventListener('keydown', onKeyDown)
    }, [active])

    useOutsideClick(ref, () => setActive(null))

    return (
        <>
            <AnimatePresence>
                {active && typeof active === 'object' && (
                    <motion.div
                        initial={{ opacity: 0 }}
                        animate={{ opacity: 1 }}
                        exit={{ opacity: 0 }}
                        className="fixed inset-0 bg-black/20 h-full w-full z-[10000]"
                    />
                )}
            </AnimatePresence>
            <AnimatePresence>
                {active && typeof active === 'object' ? (
                    <div className=" fixed inset-0 top-16 grid place-items-center z-[10001] ">
                        <motion.button
                            key={`button-${active.title}-${id}`}
                            layout
                            initial={{ opacity: 0 }}
                            animate={{ opacity: 1 }}
                            exit={{ opacity: 0, transition: { duration: 0.05 } }}
                            className="flex absolute top-2 right-2 md:right-10 lg:hidden items-center justify-center bg-white rounded-full h-6 w-6"
                            onClick={() => setActive(null)}
                        >
                            <X className="h-4 w-4 dark:text-white text-black" />
                            
                        </motion.button>
                        <motion.div
                            layoutId={`card-${active.title}-${id}`}
                            ref={ref}
                            className="w-full max-w-[500px] h-full md:h-fit md:max-h-[90%] flex flex-col bg-white dark:bg-neutral-900 sm:rounded-3xl overflow-hidden"
                        >
                            <motion.div layoutId={`image-${active.title}-${id}`}>
                                <div className="w-full h-40 md:h-50 lg:h-60 bg-blue-100 dark:bg-blue-900/20 flex items-center justify-center perspective-distant transform-3d">
                                    {active.icon ? (
                                        <div className="scale-[2] text-blue-500">{active.icon}</div>
                                    ) : (
                                        <div className="w-full h-full bg-gray-200" />
                                    )}
                                </div>
                            </motion.div>

                            <div >
                                <div className="flex justify-between p-4  items-center">
                                    <div className="">
                                        <motion.h3
                                            layoutId={`title-${active.title}-${id}`}
                                            className="font-bold text-neutral-700 dark:text-neutral-200 md:text-sm "
                                        >
                                            {active.title}
                                        </motion.h3>
                                        <motion.p
                                            layoutId={`description-${active.title}-${id}`}
                                            className="text-neutral-600 dark:text-neutral-400 text-[14px] text-balance"
                                        >
                                            {active.description}
                                        </motion.p>
                                    </div>

                                    <motion.a
                                        layoutId={`button-${active.title}-${id}`}
                                        href="#"
                                        target="_blank"
                                        className="px-4 py-3 text-sm rounded-2xl font-bold bg-blue-100 dark:bg-blue-900/30 text-white"
                                    >
                                        Visit
                                    </motion.a>
                                </div>


                             
                                  <div className="pt-4  px-4  flex justify-center mx-auto overflow-auto">
                                    <motion.div
                                        layout
                                        initial={{ opacity: 0 }}
                                        animate={{ opacity: 1 }}
                                        exit={{ opacity: 0 }}
                                        className="text-neutral-600 text-xs md:text-sm lg:text-base h-40 md:h-fit pb-5 flex flex-col items-start gap-4  dark:text-neutral-400 [mask:linear-gradient(to_bottom,white,white,transparent)] [scrollbar-width:none] [-ms-overflow-style:none] [-webkit-overflow-scrolling:touch]"
                                    >
                                        {active.content}
                                    </motion.div>
                                </div>
                              </div>
                          
                        </motion.div>
                    </div>
                ) : null}
            </AnimatePresence>
            <ul className="max-w-4xl mx-auto w-full gap-2 lg:gap-4 grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3  items-start ">
                {items.map((item) => (
                    <motion.div
                        layoutId={`card-${item.title}-${id}`}
                        key={item.id}
                        onClick={() => setActive(item)}
                        className="p-4 flex flex-col md:flex-row justify-between items-center hover:bg-neutral-50 dark:hover:bg-neutral-800 rounded-xl cursor-pointer bg-blue-50/60 dark:bg-blue-900/10 border border-blue-100 dark:border-blue-800 transition-colors"
                    >
                        <div className="flex gap-3 flex-row items-center justify-center mx-auto ">
                            <motion.div layoutId={`image-${item.title}-${id}`}>
                                <div className="h-14 w-14 rounded-lg bg-blue-100 dark:bg-blue-900/30 flex items-center justify-center text-blue-500 p-1">
                                    {item.icon}
                                </div>
                            </motion.div>
                            <div className="">
                                <motion.h3
                                    layoutId={`title-${item.title}-${id}`}
                                    className="font-medium  text-neutral-800 dark:text-neutral-200  text-left"
                                >
                                    {item.title}
                                </motion.h3>
                                <motion.p
                                    layoutId={`description-${item.title}-${id}`}
                                    className="text-neutral-600 dark:text-neutral-400 text-left  text-xs md:text-[14px]"
                                >
                                    {item.subtitle}
                                </motion.p>
                            </div>
                        </div>
                    </motion.div>
                ))}
            </ul>
        </>
    )
}

```

---

## `src\components\ui\faq-accordion.tsx`

```tsx
"use client";

import React, { useState } from "react";
import { cn } from "@/lib/utils";

export interface FaqItem {
  question: string;
  answer: React.ReactNode;
}

export interface FaqAccordionProps extends React.HTMLAttributes<HTMLDivElement> {
  items?: FaqItem[];
  title?: string;
}

const DEFAULT_ITEMS: FaqItem[] = [
  { question: "What is Vengeance UI?", answer: "Vengeance UI is a high-performance, dark-mode first component library designed for the next generation of web applications." },
  { question: "Can I use it with Tailwind CSS?", answer: "Yes! All components are built on top of Tailwind CSS and highly customizable using utility classes." },
  { question: "Are the components accessible?", answer: "Accessibility is a core focus. We ensure proper ARIA attributes, keyboard navigation, and semantic HTML structure." },
  { question: "Do I need to install a heavy npm package?", answer: "No. Vengeance UI provides a CLI that lets you copy and paste only the components you need directly into your project." },
  { question: "Is it compatible with React and Next.js?", answer: "Absolutely. The library is built with React in mind and perfectly supports Next.js Server Components and client-side rendering." },
];

export function FaqAccordion({
  items = DEFAULT_ITEMS,
  title = "Vengeance UI FAQs",
  className,
  ...props
}: FaqAccordionProps) {
  const [activeIndex, setActiveIndex] = useState<number | null>(null);

  const toggleItem = (index: number) => {
    setActiveIndex(activeIndex === index ? null : index);
  };

  return (
    <div className={cn("w-full max-w-3xl mx-auto py-8 relative font-sans", className)} {...props}>
      {title && (
        <h2 className="text-center font-bold text-2xl md:text-3xl mb-10 text-neutral-500 dark:text-neutral-400">
          {title}
        </h2>
      )}
      
      <ul className="w-full mx-auto list-none p-0 flex flex-col">
        {items.map((item, index) => {
          const isActive = activeIndex === index;
          return (
            <li
              key={index}
              className={cn(
                "w-full relative transition-all duration-300 ease-in",
                "border-b-2 border-neutral-100 dark:border-neutral-800",
                "last:border-b-0",
                isActive ? "border-b border-neutral-200 dark:border-neutral-700" : ""
              )}
            >
              <button
                className={cn(
                  "flex flex-row items-center justify-start w-full min-h-[60px] py-4 relative m-0 px-4 pl-14 cursor-pointer",
                  "border-l-[6px] md:border-l-[10px] transition-colors duration-200 text-left outline-none text-base md:text-lg",
                  isActive 
                    ? "border-l-neutral-900 dark:border-l-neutral-100 bg-neutral-100/50 dark:bg-neutral-800/30 text-neutral-900 dark:text-neutral-100 font-semibold" 
                    : "border-l-neutral-300 dark:border-l-neutral-700 bg-transparent text-neutral-600 dark:text-neutral-400 hover:border-l-neutral-500 dark:hover:border-l-neutral-500 hover:text-neutral-900 dark:hover:text-neutral-200 hover:bg-neutral-50 dark:hover:bg-neutral-900/50"
                )}
                onClick={() => toggleItem(index)}
                aria-expanded={isActive}
              >
                {/* Plus/Minus Icon */}
                <span 
                  className={cn(
                    "absolute left-4 md:left-5 top-1/2 -translate-y-1/2 transition-all duration-200 leading-none",
                    isActive ? "text-[32px] md:text-[40px] font-normal text-neutral-900 dark:text-neutral-100" : "text-[24px] md:text-[30px] font-normal text-neutral-400 dark:text-neutral-500"
                  )}
                >
                  {isActive ? "-" : "+"}
                </span>
                
                <span className="pr-8">{item.question}</span>
                
                {/* Chevron */}
                <span 
                  className={cn(
                    "absolute right-6 block w-2 h-2 border-t-[3px] border-r-[3px] transition-transform duration-200 ease-in-out",
                    isActive ? "rotate-[-44deg] border-neutral-900 dark:border-neutral-100" : "rotate-[133deg] border-neutral-400 dark:border-neutral-500"
                  )}
                />
              </button>

              <div 
                className={cn(
                  "grid transition-all duration-300 ease-in-out w-full",
                  "border-l-[6px] md:border-l-[10px]",
                  isActive ? "grid-rows-[1fr] border-l-neutral-900 dark:border-l-neutral-100 bg-neutral-100/50 dark:bg-neutral-800/30" : "grid-rows-[0fr] border-l-neutral-300 dark:border-l-neutral-700 bg-transparent"
                )}
              >
                <div className="overflow-hidden">
                  <div className="flex flex-row items-start justify-start w-full px-4 pl-14 pb-6 pt-2 text-base md:text-lg font-normal text-neutral-700 dark:text-neutral-300">
                    <span className="opacity-90">{item.answer}</span>
                  </div>
                </div>
              </div>
            </li>
          );
        })}
      </ul>
    </div>
  );
}

export default FaqAccordion;

```

---

## `src\components\ui\field.tsx`

```tsx
"use client"

import { useMemo } from "react"
import { cva, type VariantProps } from "class-variance-authority"

import { cn } from "@/lib/utils"
import { Label } from "@/components/ui/label"
import { Separator } from "@/components/ui/separator"

function FieldSet({ className, ...props }: React.ComponentProps<"fieldset">) {
  return (
    <fieldset
      data-slot="field-set"
      className={cn(
        "flex flex-col gap-6",
        "has-[>[data-slot=checkbox-group]]:gap-3 has-[>[data-slot=radio-group]]:gap-3",
        className
      )}
      {...props}
    />
  )
}

function FieldLegend({
  className,
  variant = "legend",
  ...props
}: React.ComponentProps<"legend"> & { variant?: "legend" | "label" }) {
  return (
    <legend
      data-slot="field-legend"
      data-variant={variant}
      className={cn(
        "mb-3 font-medium",
        "data-[variant=legend]:text-base",
        "data-[variant=label]:text-sm",
        className
      )}
      {...props}
    />
  )
}

function FieldGroup({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="field-group"
      className={cn(
        "group/field-group @container/field-group flex w-full flex-col gap-7 data-[slot=checkbox-group]:gap-3 [&>[data-slot=field-group]]:gap-4",
        className
      )}
      {...props}
    />
  )
}

const fieldVariants = cva(
  "group/field flex w-full gap-3 data-[invalid=true]:text-destructive",
  {
    variants: {
      orientation: {
        vertical: ["flex-col [&>*]:w-full [&>.sr-only]:w-auto"],
        horizontal: [
          "flex-row items-center",
          "[&>[data-slot=field-label]]:flex-auto",
          "has-[>[data-slot=field-content]]:items-start has-[>[data-slot=field-content]]:[&>[role=checkbox],[role=radio]]:mt-px",
        ],
        responsive: [
          "flex-col [&>*]:w-full [&>.sr-only]:w-auto @md/field-group:flex-row @md/field-group:items-center @md/field-group:[&>*]:w-auto",
          "@md/field-group:[&>[data-slot=field-label]]:flex-auto",
          "@md/field-group:has-[>[data-slot=field-content]]:items-start @md/field-group:has-[>[data-slot=field-content]]:[&>[role=checkbox],[role=radio]]:mt-px",
        ],
      },
    },
    defaultVariants: {
      orientation: "vertical",
    },
  }
)

function Field({
  className,
  orientation = "vertical",
  ...props
}: React.ComponentProps<"div"> & VariantProps<typeof fieldVariants>) {
  return (
    <div
      role="group"
      data-slot="field"
      data-orientation={orientation}
      className={cn(fieldVariants({ orientation }), className)}
      {...props}
    />
  )
}

function FieldContent({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="field-content"
      className={cn(
        "group/field-content flex flex-1 flex-col gap-1.5 leading-snug",
        className
      )}
      {...props}
    />
  )
}

function FieldLabel({
  className,
  ...props
}: React.ComponentProps<typeof Label>) {
  return (
    <Label
      data-slot="field-label"
      className={cn(
        "group/field-label peer/field-label flex w-fit gap-2 leading-snug group-data-[disabled=true]/field:opacity-50",
        "has-[>[data-slot=field]]:w-full has-[>[data-slot=field]]:flex-col has-[>[data-slot=field]]:rounded-md has-[>[data-slot=field]]:border [&>*]:data-[slot=field]:p-4",
        "has-data-[state=checked]:bg-primary/5 has-data-[state=checked]:border-primary dark:has-data-[state=checked]:bg-primary/10",
        className
      )}
      {...props}
    />
  )
}

function FieldTitle({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="field-label"
      className={cn(
        "flex w-fit items-center gap-2 text-sm leading-snug font-medium group-data-[disabled=true]/field:opacity-50",
        className
      )}
      {...props}
    />
  )
}

function FieldDescription({ className, ...props }: React.ComponentProps<"p">) {
  return (
    <p
      data-slot="field-description"
      className={cn(
        "text-muted-foreground text-sm leading-normal font-normal group-has-[[data-orientation=horizontal]]/field:text-balance",
        "last:mt-0 nth-last-2:-mt-1 [[data-variant=legend]+&]:-mt-1.5",
        "[&>a:hover]:text-primary [&>a]:underline [&>a]:underline-offset-4",
        className
      )}
      {...props}
    />
  )
}

function FieldSeparator({
  children,
  className,
  ...props
}: React.ComponentProps<"div"> & {
  children?: React.ReactNode
}) {
  return (
    <div
      data-slot="field-separator"
      data-content={!!children}
      className={cn(
        "relative -my-2 h-5 text-sm group-data-[variant=outline]/field-group:-mb-2",
        className
      )}
      {...props}
    >
      <Separator className="absolute inset-0 top-1/2" />
      {children && (
        <span
          className="bg-background text-muted-foreground relative mx-auto block w-fit px-2"
          data-slot="field-separator-content"
        >
          {children}
        </span>
      )}
    </div>
  )
}

function FieldError({
  className,
  children,
  errors,
  ...props
}: React.ComponentProps<"div"> & {
  errors?: Array<{ message?: string } | undefined>
}) {
  const content = useMemo(() => {
    if (children) {
      return children
    }

    if (!errors?.length) {
      return null
    }

    const uniqueErrors = [
      ...new Map(errors.map((error) => [error?.message, error])).values(),
    ]

    if (uniqueErrors?.length == 1) {
      return uniqueErrors[0]?.message
    }

    return (
      <ul className="ml-4 flex list-disc flex-col gap-1">
        {uniqueErrors.map(
          (error, index) =>
            error?.message && <li key={index}>{error.message}</li>
        )}
      </ul>
    )
  }, [children, errors])

  if (!content) {
    return null
  }

  return (
    <div
      role="alert"
      data-slot="field-error"
      className={cn("text-destructive text-sm font-normal", className)}
      {...props}
    >
      {content}
    </div>
  )
}

export {
  Field,
  FieldLabel,
  FieldDescription,
  FieldError,
  FieldGroup,
  FieldLegend,
  FieldSeparator,
  FieldSet,
  FieldContent,
  FieldTitle,
}

```

---

## `src\components\ui\flip-fade-text.tsx`

```tsx
"use client"

import { useEffect, useState, useMemo, useCallback, memo } from "react"
import { motion, AnimatePresence } from "framer-motion"
import { cn } from "@/lib/utils"

interface FlipFadeTextProps {
    /**
     * Array of words to cycle through
     * @default ["LOADING", "COMPUTING", "SEARCHING", "RETRIEVING", "ASSEMBLING"]
     */
    words?: string[]
    /**
     * Interval between word changes in milliseconds
     * @default 2500
     */
    interval?: number
    /**
     * Additional CSS classes for the container
     */
    className?: string
    /**
     * Additional CSS classes for the text
     */
    textClassName?: string
    /**
     * Animation duration for each letter in seconds
     * @default 0.6
     */
    letterDuration?: number
    /**
     * Stagger delay between letters on enter in seconds
     * @default 0.1
     */
    staggerDelay?: number
    /**
     * Stagger delay between letters on exit in seconds
     * @default 0.05
     */
    exitStaggerDelay?: number
}

const defaultWords = ["LOADING", "COMPUTING", "SEARCHING", "RETRIEVING", "ASSEMBLING"]

// Memoized Letter component for performance
const Letter = memo(function Letter({
    char,
    letterDuration
}: {
    char: string
    letterDuration: number
}) {
    return (
        <motion.span
            style={{ transformStyle: "preserve-3d" }}
            variants={{
                initial: {
                    rotateX: 90,
                    y: 20,
                    opacity: 0,
                    filter: "blur(8px)",
                },
                animate: {
                    rotateX: 0,
                    y: 0,
                    opacity: 1,
                    filter: "blur(0px)",
                    transition: {
                        duration: letterDuration,
                        ease: [0.2, 0.65, 0.3, 0.9],
                    },
                },
                exit: {
                    rotateX: -90,
                    y: -20,
                    opacity: 0,
                    filter: "blur(8px)",
                    transition: {
                        duration: letterDuration * 0.67,
                        ease: "easeIn",
                    },
                },
            }}
            className="inline-block"
        >
            {char}
        </motion.span>
    )
})

// Memoized Word component for performance
const Word = memo(function Word({
    text,
    staggerDelay,
    exitStaggerDelay,
    letterDuration,
    textClassName
}: {
    text: string
    staggerDelay: number
    exitStaggerDelay: number
    letterDuration: number
    textClassName?: string
}) {
    const letters = useMemo(() => text.split(""), [text])

    return (
        <motion.div
            className={cn(
                "flex gap-[0.1em] text-4xl md:text-6xl font-bold uppercase tracking-wider text-neutral-800 dark:text-neutral-100",
                textClassName
            )}
            initial="initial"
            animate="animate"
            exit="exit"
            variants={{
                initial: { opacity: 1 },
                animate: {
                    opacity: 1,
                    transition: {
                        staggerChildren: staggerDelay,
                    },
                },
                exit: {
                    opacity: 1,
                    transition: {
                        staggerChildren: exitStaggerDelay,
                    },
                },
            }}
        >
            {letters.map((char, i) => (
                <Letter
                    key={`${char}-${i}`}
                    char={char}
                    letterDuration={letterDuration}
                />
            ))}
        </motion.div>
    )
})

export function FlipFadeText({
    words = defaultWords,
    interval = 2500,
    className,
    textClassName,
    letterDuration = 0.6,
    staggerDelay = 0.1,
    exitStaggerDelay = 0.05,
}: FlipFadeTextProps) {
    const [index, setIndex] = useState(0)

    // Memoize the interval callback
    const updateIndex = useCallback(() => {
        setIndex((prev) => (prev + 1) % words.length)
    }, [words.length])

    useEffect(() => {
        const timer = setInterval(updateIndex, interval)
        return () => clearInterval(timer)
    }, [updateIndex, interval])

    // Memoize the current word
    const currentWord = useMemo(() => words[index], [words, index])

    return (
        <div className={cn("flex items-center justify-center min-h-[200px]", className)}>
            <div className="relative flex items-center justify-center" style={{ perspective: "1000px" }}>
                <AnimatePresence mode="wait">
                    <Word
                        key={currentWord}
                        text={currentWord}
                        staggerDelay={staggerDelay}
                        exitStaggerDelay={exitStaggerDelay}
                        letterDuration={letterDuration}
                        textClassName={textClassName}
                    />
                </AnimatePresence>
            </div>
        </div>
    )
}

export default FlipFadeText

```

---

## `src\components\ui\flip-text.tsx`

```tsx
"use client";

import React, { useMemo } from "react";
import { cn } from "@/lib/utils";

interface FlipTextProps {
    /**
     * Additional CSS classes for the wrapper
     */
    className?: string;

    /**
     * The text content to animate (will be split by spaces)
     */
    children: string;

    /**
     * Duration of the flip animation in seconds
     * @default 2.2
     */
    duration?: number;

    /**
     * Initial delay before animation starts in seconds
     * @default 0
     */
    delay?: number;

    /**
     * Whether the animation should loop infinitely
     * @default true
     */
    loop?: boolean;

    /**
     * Custom separator for splitting text (default is space)
     * @default " "
     */
    separator?: string;

    /**
     * Whether all characters should animate together (no stagger)
     * @default false
     */
    together?: boolean;
}

export function FlipText({
    className,
    children,
    duration = 2.2,
    delay = 0,
    loop = true,
    separator = " ",
    together = false,
}: FlipTextProps) {
    const words = useMemo(() => children.split(separator), [children, separator]);
    const totalChars = children.length;

    // Calculate character index for each position
    const getCharIndex = (wordIndex: number, charIndex: number) => {
        let index = 0;
        for (let i = 0; i < wordIndex; i++) {
            index += words[i].length + (separator === " " ? 1 : separator.length);
        }
        return index + charIndex;
    };

    return (
        <div
            className={cn(
                "flip-text-wrapper inline-block leading-none",
                className
            )}
            style={{ perspective: "1000px" }}
        >
            {words.map((word, wordIndex) => {
                const chars = word.split("");

                return (
                    <span
                        key={wordIndex}
                        className="word inline-block whitespace-nowrap"
                        style={{ transformStyle: "preserve-3d" }}
                    >
                        {chars.map((char, charIndex) => {
                            const currentGlobalIndex = getCharIndex(wordIndex, charIndex);

                            // Calculate delay - if together, use same delay for all
                            let calculatedDelay = delay;
                            if (!together) {
                                const normalizedIndex = currentGlobalIndex / totalChars;
                                const sineValue = Math.sin(normalizedIndex * (Math.PI / 2));
                                calculatedDelay = sineValue * (duration * 0.25) + delay;
                            }

                            return (
                                <span
                                    key={charIndex}
                                    className="flip-char inline-block relative"
                                    data-char={char}
                                    style={
                                        {
                                            "--flip-duration": `${duration}s`,
                                            "--flip-delay": `${calculatedDelay}s`,
                                            "--flip-iteration": loop ? "infinite" : "1",
                                            transformStyle: "preserve-3d",
                                        } as React.CSSProperties
                                    }
                                >
                                    {char}
                                </span>
                            );
                        })}
                        {separator === " " && wordIndex < words.length - 1 && (
                            <span className="whitespace inline-block">&nbsp;</span>
                        )}
                        {separator !== " " && wordIndex < words.length - 1 && (
                            <span className="separator inline-block">{separator}</span>
                        )}
                    </span>
                );
            })}
        </div>
    );
}

export default FlipText;

```

---

## `src\components\ui\fluid-morph-bg.tsx`

```tsx
"use client";

import React from "react";
import { motion } from "framer-motion";
import { cn } from "@/lib/utils";

interface FluidMorphBgProps {
  /**
   * Additional CSS classes for the container.
   */
  className?: string;
  /**
   * The duration of the morphing animation in seconds.
   */
  duration?: number;
  /**
   * The base color palette to use for the shapes.
   * Array of hex colors for each path.
   */
  colors?: string[];
  /**
   * Optional background color for the scene container.
   */
  backgroundColor?: string;
}

export function FluidMorphBg({
  className,
  duration = 4,
  colors = [
    "#4f4fea",
    "#0c27cf",
    "#13269c",
    "#242468",
    "#2648e6",
    "#2c31b0",
    "#262689",
  ],
  backgroundColor = "#282886",
}: FluidMorphBgProps) {
  // SVG viewbox dimensions
  const viewBox = "0 0 1440 800";

  // Data for each morphing path
  // path[0]: original "d", path[1]: "pathdata:id" (target morph shape)
  const pathsData = [
    [
      "M -84.52,-81.13 C -94.62,-73.4 -88.88,-59.55 -90.33,-48.91 -89.29,27.31 -89.61,103.5 -88.33,179.8 -85.99,416.1 -81.32,888.9 -81.32,888.9 -81.32,888.9 974.5,888.7 1587,891.9 1518,719.9 1487,644 1429,533 1388,437.7 1447,259.7 1400,187 1362,132 1270,90.53 1207,39.93 1161,2.932 1071,-74.45 1071,-74.45 1071,-74.45 914.5,-77.77 848.2,-80.17 537.6,-80.84 227,-81.38 -83.6,-81.6 -83.91,-81.44 -84.21,-81.29 -84.52,-81.13 Z",
      "M -84.52,-81.13 C -94.62,-73.4 -88.88,-59.55 -90.33,-48.91 -89.29,27.31 -89.61,103.5 -88.33,179.8 -85.99,416.1 -81.32,888.9 -81.32,888.9 -81.32,888.9 974.5,888.7 1587,891.9 1576,704.7 1517,625.7 1459,514.7 1418,419.4 1430,288.5 1382,187 1349,116.3 1296,54.47 1240,0.3429 1205,-33.51 1120,-83.59 1120,-83.59 1120,-83.59 914.5,-77.77 848.2,-80.17 537.6,-80.84 227,-81.38 -83.6,-81.6 -83.91,-81.44 -84.21,-81.29 -84.52,-81.13 Z",
    ],
    [
      "M 665.2,-83.08 C 413.7,-81.89 162.2,-82.43 -89.29,-81.61 -90.35,164.3 -85.06,410.2 -84.09,656.1 -83.37,733.7 -82.64,811.3 -81.92,888.9 442.4,889.8 966.7,890.7 1491,891.6 1253,747.5 1417,429.4 1286,245.4 1227,163.2 1107,142.1 1043,64.54 1009,24.41 973,-76.01 973,-76.01 973,-76.01 706.6,-83.67 665.2,-83.08 Z",
      "M 665.2,-83.08 C 413.7,-81.89 162.2,-82.43 -89.29,-81.61 -90.35,164.3 -85.06,410.2 -84.09,656.1 -83.37,733.7 -82.64,811.3 -81.92,888.9 442.4,889.8 966.7,890.7 1491,891.6 1253,747.5 1349,460.4 1243,260.6 1199,176.6 1145,96.92 1083,24.95 1050,-12.63 973,-76.01 973,-76.01 973,-76.01 706.6,-83.67 665.2,-83.08 Z",
    ],
    [
      "M -85.01,-74.02 C -92.39,-66.64 -85.37,-55.79 -87.81,-46.91 -86.65,265.1 -84.66,577.2 -83.18,889.2 317.2,888.3 717.5,885.8 1118,890.4 1152,890.6 1187,890.9 1221,890 1219,768.3 1224,643.6 1187,526 1153,417 1091,319.3 1029,224.1 998.8,178.5 968.8,132.6 936.6,88.23 891.7,27.39 772.2,-78.96 772.2,-78.96 772.2,-78.96 222.1,-81.07 -85.01,-74.02 Z",
      "M -85.01,-74.02 C -92.39,-66.64 -85.37,-55.79 -87.81,-46.91 -86.65,265.1 -84.66,577.2 -83.18,889.2 317.2,888.3 717.5,885.8 1118,890.4 1152,890.6 1187,890.9 1221,890 1219,768.3 1175,659.3 1150,544.3 1128,438.4 1143,312.6 1081,227.1 1004,121.1 925.8,114.8 851.3,54.73 762,-17.34 772.2,-78.96 772.2,-78.96 772.2,-78.96 222.1,-81.07 -85.01,-74.02 Z",
    ],
    [
      "M -92.42,-79.11 C -89.97,243.8 -87.52,566.7 -85.07,889.6 201.8,889.9 488.7,889.9 775.5,895.6 880.4,896.9 985.2,894 1090,892.5 1064,773.3 1037,651.6 976.1,544.8 946.7,495.8 914.6,448.3 882,401.3 820.9,314.4 742.3,252 666.4,177.4 583.2,98.01 496.5,12.18 386.7,-23.38 328.4,-45.64 232.6,-81.38 232.6,-81.38 232.6,-81.38 9.82,-84.94 -92.42,-79.11 Z",
      "M -92.42,-79.11 C -89.97,243.8 -87.52,566.7 -85.07,889.6 201.8,889.9 488.7,889.9 775.5,895.6 880.4,896.9 1063,889.5 1063,889.5 1063,889.5 1081,768.2 997.4,608.7 958.5,534.8 969.9,436.8 918.5,370.8 848.4,280.8 717,260.3 629.9,186.5 552.6,121.2 491.5,38.73 426.3,-38.61 412.9,-54.44 387.9,-87.47 387.9,-87.47 387.9,-87.47 9.82,-84.94 -92.42,-79.11 Z",
    ],
    [
      "M -88.6,95.54 C -90.38,166.1 -88.23,236.7 -88.68,307.4 L -86.19,890 C 229.7,890.2 939.8,892.4 939.8,892.4 855.2,767 831,639.4 721.4,519.4 634.7,424.5 526.4,360.9 428.8,281.8 332.7,204 251.6,102.3 140.1,48.9 70.75,15.73 -24.82,24.2 -85.28,0.03 Z",
      "M -88.6,95.54 C -90.38,166.1 -88.23,236.7 -88.68,307.4 L -86.19,890 C 229.7,890.2 939.8,892.4 939.8,892.4 906.9,734.7 779.3,676 721.4,519.4 676.8,398.8 566.5,307.1 458.9,236.6 355.2,168.7 220.3,165.7 107.8,113.5 40.05,82.12 -24.82,24.2 -85.28,0.03 Z",
    ],
    [
      "M -95.69,252.3 -87.65,890.4 698.1,892 C 698.1,892 599.1,687.7 518.9,610.6 348,446.2 131.4,466.5 -95.69,252.3 Z",
      "M -95.69,252.3 -87.65,890.4 698.1,892 C 698.1,892 569.8,587.1 448.9,482.7 299.7,353.9 131.4,466.5 -95.69,252.3 Z",
    ],
    [
      "M -85.59,444.4 -85.59,890.6 489,895.6 C 489,895.6 436.8,745.3 382.5,690.8 258.1,565.8 57.98,629.2 -85.59,444.4 Z",
      "M -85.59,444.4 -85.59,890.6 546.9,895.6 C 546.9,895.6 517.4,695.4 339.9,593.4 187.7,505.9 57.98,629.2 -85.59,444.4 Z",
    ],
  ];

  return (
    <div
      className={cn("relative overflow-hidden w-full h-full", className)}
      style={{ backgroundColor }}
    >
      <svg
        className="absolute inset-0 w-full h-full object-cover"
        preserveAspectRatio="none"
        viewBox={viewBox}
      >
        {pathsData.map((dList, index) => {
          // Each path gets a random duration variation for more organic feel (like original script did)
          // To keep it simple in React/Framer Motion and avoid hydration issues, 
          // we use the duration prop as a base and vary slightly based on index.
          const variance = (index % 3) * 0.5; // add 0, 0.5, or 1 sec
          const pathDuration = duration + variance;
          const delay = (index % 4) * 0.2; // slight delay offsets

          return (
            <motion.path
              key={index}
              d={dList[0]}
              fill={colors[index % colors.length]}
              animate={{
                d: dList,
              }}
              transition={{
                duration: pathDuration,
                repeat: Infinity,
                repeatType: "mirror",
                ease: "easeInOut",
                delay: delay,
              }}
            />
          );
        })}
      </svg>
    </div>
  );
}

```

---

## `src\components\ui\folder-preview.tsx`

```tsx
"use client";

import * as React from "react";
import { motion } from "framer-motion";
import { cn } from "@/lib/utils";

// ============================================
// SVG Icon Components
// ============================================

const FolderBackIcon = ({ className }: { className?: string }) => (
    <svg viewBox="0 0 20 16" className={cn("w-full h-full fill-current", className)}>
        <path d="M7.5,0C7.4,0,2,0,2,0C0.9,0,0,0.9,0,2l0,12c0,1.1,0.9,2,2,2h16c1.1,0,2-0.9,2-2V4c0-1.1-0.9-2-2-2c0,0-7.5,0-8,0C9,2,9.9,0,7.5,0z" />
    </svg>
);

const FolderCoverIcon = ({ className }: { className?: string }) => (
    <svg viewBox="0 0 20 16" className={cn("w-full h-full fill-current", className)}>
        <path d="M2,2h16c1.1,0,2,0.9,2,2v10c0,1.1-0.9,2-2,2H2c-1.1,0-2-0.9-2-2V4C0,2.9,0.9,2,2,2z" />
    </svg>
);

const UsersIcon = ({ className }: { className?: string }) => (
    <svg viewBox="0 0 24 24" className={cn("w-full h-full fill-current", className)}>
        <path
            opacity="0.3"
            d="M22.2,17.7l-4-2c-0.5-0.3-0.8-0.8-0.8-1.3v-1.6c0.1-0.1,0.2-0.3,0.4-0.5c0.5-0.8,0.9-1.6,1.2-2.5c0.5-0.2,0.9-0.6,0.9-1.2V7c0-0.4-0.2-0.7-0.4-0.9V3.7c0,0,0.5-3.7-4.6-3.7c-5,0-4.6,3.7-4.6,3.7v2.4C10.1,6.3,9.9,6.7,9.9,7v1.7c0,0.4,0.2,0.8,0.6,1c0.4,1.8,1.5,3.1,1.5,3.1v1.5c0,0.6-0.3,1.1-0.8,1.3l-3.7,2c-1.1,0.6-1.7,1.7-1.7,2.9v1.3H24v-1.3C24,19.4,23.3,18.3,22.2,17.7z"
        />
        <path
            opacity="0.5"
            d="M7.5,17.7l2.5-1.3c0,0,0,0,0,0l1.2-0.7c0.5-0.3,0.8-0.8,0.8-1.3v-1.5c0,0-0.4-0.5-0.9-1.4l0,0c0,0,0,0,0,0c-0.1-0.1-0.1-0.2-0.2-0.3c0,0,0,0,0-0.1c-0.1-0.1-0.1-0.3-0.2-0.4c0,0,0,0,0,0c0-0.1-0.1-0.2-0.1-0.4c0,0,0-0.1,0-0.1c0-0.1-0.1-0.3-0.1-0.4c-0.3-0.2-0.6-0.6-0.6-1V7c0-0.4,0.2-0.7,0.4-0.9V3.8C9.8,3.3,8.9,2.9,7.4,2.9c-4,0-4.1,3.3-4.1,3.3v2.1C3.1,8.5,2.9,8.8,2.9,9.1v1.4c0,0.4,0.2,0.7,0.5,0.9c0.4,1.6,1.6,2.7,1.6,2.7v1.3c0,0.5-0.3,0.9-0.7,1.1l-2.8,1.7C0.6,18.8,0,19.7,0,20.8v1.2h5.8v-1.3C5.8,19.4,6.5,18.3,7.5,17.7z"
        />
    </svg>
);

const GlobeIcon = ({ className }: { className?: string }) => (
    <svg viewBox="0 0 24 24" className={cn("w-full h-full fill-current", className)}>
        <circle cx="12" cy="12" r="10" opacity="0.3" />
        <path d="M12,2C6.5,2,2,6.5,2,12s4.5,10,10,10s10-4.5,10-10S17.5,2,12,2z M12,20c-4.4,0-8-3.6-8-8s3.6-8,8-8s8,3.6,8,8S16.4,20,12,20z" />
    </svg>
);

const PadlockIcon = ({ className }: { className?: string }) => (
    <svg viewBox="0 0 24 33.6" className={cn("w-full h-full fill-current", className)}>
        <path d="M23,13.5h-1.7V9.4C21.4,4.2,17.2,0,12,0C6.8,0,2.6,4.2,2.6,9.4v4.1H1c-0.5,0-1,0.4-1,1v18.2c0,0.5,0.4,1,1,1H23c0.5,0,1-0.4,1-1V14.4C24,13.9,23.6,13.5,23,13.5z M13.5,24.5v3.9c0,0.3-0.3,0.6-0.6,0.6h-1.8c-0.3,0-0.6-0.3-0.6-0.6v-3.9c-0.7-0.5-1.1-1.3-1.1-2.1c0-1.4,1.2-2.6,2.6-2.6c1.4,0,2.6,1.2,2.6,2.6C14.6,23.3,14.2,24.1,13.5,24.5z M16.9,13.5H7.1V9.4c0-2.7,2.2-4.9,4.9-4.9c2.7,0,4.9,2.2,4.9,4.9V13.5z" />
    </svg>
);

const CloudIcon = ({ className }: { className?: string }) => (
    <svg viewBox="0 0 24 22.2" className={cn("w-full h-full fill-current", className)}>
        <path d="M19.5,5.8c-0.3-1.5-1-2.9-2.2-4c-1.3-1.2-3-1.8-4.7-1.8C11.3,0,10,0.4,8.9,1.1C8,1.7,7.2,2.5,6.6,3.5c-0.2,0-0.5-0.1-0.7-0.1c-2.1,0-3.8,1.7-3.8,3.8c0,0.3,0,0.5,0.1,0.8C0.8,9,0,10.6,0,12.3C0,13.6,0.5,15,1.4,16c1,1.1,2.2,1.7,3.6,1.8c0,0,0,0,0,0h4.2c0.4,0,0.7-0.3,0.7-0.7s-0.3-0.7-0.7-0.7H5c-2-0.1-3.7-2-3.7-4.2c0-1.4,0.8-2.7,2-3.4c0.3-0.2,0.4-0.5,0.3-0.8C3.5,7.8,3.4,7.5,3.4,7.2c0-1.4,1.1-2.5,2.5-2.5c0.3,0,0.6,0,0.8,0.1c0.3,0.1,0.7,0,0.8-0.3c0.9-2,2.9-3.2,5.1-3.2c2.9,0,5.3,2.2,5.6,5.1c0,0.3,0.3,0.5,0.6,0.6c2.2,0.4,3.9,2.4,3.9,4.7c0,2.5-1.9,4.6-4.3,4.8h-3.6c-0.4,0-0.7,0.3-0.7,0.7s0.3,0.7,0.7,0.7h3.7c0,0,0,0,0,0c1.5-0.1,2.9-0.8,4-2c1-1.1,1.6-2.6,1.6-4.1C24,8.9,22.1,6.5,19.5,5.8z M16,12.9c0.3-0.3,0.3-0.7,0-0.9l-3.5-3.5c-0.1-0.1-0.3-0.2-0.5-0.2c-0.2,0-0.3,0.1-0.5,0.2L8,12c-0.3,0.3-0.3,0.7,0,0.9c0.1,0.1,0.3,0.2,0.5,0.2c0.2,0,0.3-0.1,0.5-0.2l2.4-2.4v11c0,0.4,0.3,0.7,0.7,0.7s0.7-0.3,0.7-0.7v-11l2.4,2.4C15.3,13.2,15.7,13.2,16,12.9z" />
    </svg>
);

const FileIcon = ({ className }: { className?: string }) => (
    <svg viewBox="0 0 20 26.8" className={cn("w-full h-full fill-current", className)}>
        <path d="M2.3,0C1,0,0,1,0,2.3v22.2c0,1.2,1,2.3,2.3,2.3h15.4c1.2,0,2.3-1,2.3-2.3V6l-6-6H2.3z" />
        <path opacity="0.1" d="M13.9,3.7V0l6,6h-3.7C14.9,6,13.9,5,13.9,3.7z" />
    </svg>
);

// ============================================
// Types & Interfaces
// ============================================

export type FolderVariant =
    | "devi"
    | "rudras"
    | "ardra"
    | "shakti"
    | "kubera"
    | "hari"
    | "ravi"
    | "durga"
    | "nandi";

export interface FolderPreviewProps {
    variant?: FolderVariant;
    images?: string[];
    files?: { name: string; type?: "txt" | "gif" | "mp3" | "default" }[];
    label?: string;
    size?: "sm" | "md" | "lg";
    className?: string;
    onClick?: () => void;
}

// ============================================
// Color Schemes for Each Variant
// ============================================

const variantColors: Record<
    FolderVariant,
    {
        back: string;
        cover: string;
        deco: string;
        caption: string;
        bg: string;
    }
> = {
    devi: {
        back: "text-gray-500",
        cover: "text-gray-400",
        deco: "text-gray-400 brightness-125",
        caption: "text-gray-800 dark:text-gray-200",
        bg: "bg-gray-100 dark:bg-gray-900",
    },
    rudras: {
        back: "text-gray-700 dark:text-gray-600",
        cover: "text-gray-600 dark:text-gray-500",
        deco: "text-gray-400",
        caption: "text-blue-600 dark:text-blue-400",
        bg: "bg-slate-200 dark:bg-slate-800",
    },
    ardra: {
        back: "text-blue-800 dark:text-blue-700",
        cover: "text-blue-600 dark:text-blue-500",
        deco: "text-blue-700 dark:text-blue-600",
        caption: "text-blue-500 dark:text-blue-400",
        bg: "bg-gray-800 dark:bg-gray-950",
    },
    shakti: {
        back: "text-indigo-800",
        cover: "text-indigo-700",
        deco: "text-indigo-800",
        caption: "text-green-400",
        bg: "bg-blue-600 dark:bg-blue-800",
    },
    kubera: {
        back: "text-gray-900",
        cover: "text-gray-700",
        deco: "text-gray-600",
        caption: "text-gray-900 dark:text-gray-100",
        bg: "bg-emerald-400 dark:bg-emerald-600",
    },
    hari: {
        back: "text-blue-800",
        cover: "text-blue-700",
        deco: "text-blue-800",
        caption: "text-yellow-400",
        bg: "bg-sky-500 dark:bg-sky-700",
    },
    ravi: {
        back: "text-gray-900",
        cover: "text-gray-700",
        deco: "text-black dark:text-white",
        caption: "text-gray-900 dark:text-gray-100",
        bg: "bg-gray-200 dark:bg-gray-800",
    },
    durga: {
        back: "text-green-600",
        cover: "text-green-500",
        deco: "text-green-600",
        caption: "text-green-400 font-mono",
        bg: "bg-gray-900 dark:bg-black",
    },
    nandi: {
        back: "text-amber-500",
        cover: "text-amber-400",
        deco: "text-amber-500",
        caption: "text-gray-900 dark:text-gray-100",
        bg: "bg-green-100 dark:bg-green-950",
    },
};

// ============================================
// Size Configuration
// ============================================

const sizeConfig = {
    sm: {
        folder: "w-16",
        thumb: "w-10 h-10",
        deco: "w-4 h-4",
        caption: "text-xs",
    },
    md: {
        folder: "w-24",
        thumb: "w-14 h-14",
        deco: "w-6 h-6",
        caption: "text-sm",
    },
    lg: {
        folder: "w-32",
        thumb: "w-20 h-20",
        deco: "w-8 h-8",
        caption: "text-base",
    },
};

// ============================================
// Animation Variants
// ============================================

const createCircularPositions = (count: number, radius: number = 120) => {
    return Array.from({ length: count }, (_, i) => {
        const startAngle = Math.PI / count;
        const angle = startAngle / 2 + startAngle * i;
        return {
            x: Math.round(radius * Math.cos(angle)),
            y: Math.round(-radius * Math.sin(angle)),
        };
    });
};

// ============================================
// Individual Folder Components for each Variant
// ============================================

const DeviFolder: React.FC<{
    images: string[];
    isHovered: boolean;
    colors: typeof variantColors.devi;
    sizes: typeof sizeConfig.md;
    label?: string;
}> = ({ images, isHovered, colors, sizes, label }) => {
    const positions = createCircularPositions(images.length, 80);

    return (
        <div className="relative">
            {/* Previews - positioned from folder center */}
            <div className="absolute inset-0 flex items-center justify-center pointer-events-none z-10">
                {images.map((img, i) => (
                    <motion.img
                        key={i}
                        src={img}
                        alt=""
                        className="absolute w-12 h-12 object-cover rounded-full border-2 border-white shadow-md"
                        initial={{ opacity: 0, scale: 0.7, x: 0, y: 0 }}
                        animate={
                            isHovered
                                ? {
                                    opacity: 1,
                                    scale: 1,
                                    x: positions[i]?.x || 0,
                                    y: positions[i]?.y || 0,
                                }
                                : { opacity: 0, scale: 0.7, x: 0, y: 0 }
                        }
                        transition={{
                            duration: 0.6,
                            delay: (images.length - i - 1) * 0.04,
                            ease: [0.2, 1, 0.3, 1],
                        }}
                    />
                ))}
            </div>

            {/* Folder */}
            <div className="relative cursor-pointer aspect-[20/16]" style={{ perspective: "800px" }}>
                {/* Back */}
                <div className={cn("absolute inset-0 transition-colors duration-150", colors.back)}>
                    <FolderBackIcon />
                </div>

                {/* Cover */}
                <motion.div
                    className={cn(
                        "relative transition-colors duration-150",
                        isHovered ? "text-gray-600" : colors.cover
                    )}
                >
                    <FolderCoverIcon />
                    <div
                        className={cn(
                            "absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2",
                            sizes.deco,
                            colors.deco
                        )}
                    >
                        <UsersIcon />
                    </div>
                </motion.div>
            </div>

            {label && (
                <h3 className={cn("mt-3 font-medium text-center", sizes.caption, colors.caption)}>
                    {label}
                </h3>
            )}
        </div>
    );
};


const RudrasFolder: React.FC<{
    images: string[];
    isHovered: boolean;
    colors: typeof variantColors.rudras;
    sizes: typeof sizeConfig.md;
    label?: string;
}> = ({ images, isHovered, colors, sizes, label }) => {
    const positions = createCircularPositions(images.length, 80);

    return (
        <div className="relative">
            {/* Previews - positioned from folder center */}
            <div className="absolute inset-0 flex items-center justify-center pointer-events-none z-10">
                {images.map((img, i) => (
                    <motion.img
                        key={i}
                        src={img}
                        alt=""
                        className="absolute w-12 h-12 object-cover rounded-full border-2 border-white shadow-md"
                        initial={{ opacity: 0, scale: 0, x: 0, y: 0 }}
                        animate={
                            isHovered
                                ? {
                                    opacity: 1,
                                    scale: 1,
                                    x: -positions[i]?.x || 0,
                                    y: positions[i]?.y || 0,
                                }
                                : { opacity: 0, scale: 0, x: 0, y: 0 }
                        }
                        transition={{
                            duration: 0.8,
                            delay: (images.length - i - 1) * 0.08,
                            type: "spring",
                            stiffness: 200,
                            damping: 15,
                        }}
                    />
                ))}
            </div>

            {/* Folder */}
            <div className="relative cursor-pointer aspect-[20/16]" style={{ perspective: "800px" }}>
                {/* Back */}
                <div className={cn("absolute inset-0", colors.back)}>
                    <FolderBackIcon />
                </div>

                {/* Paper Sheet Deco */}
                <div className="absolute bottom-0.5 left-0.5 right-0.5 h-3/4 bg-white dark:bg-gray-200 rounded-lg" />

                {/* Cover */}
                <motion.div
                    className={cn("relative", colors.cover)}
                    style={{ transformOrigin: "50% 100%", transformStyle: "preserve-3d" }}
                    animate={isHovered ? { rotateX: -30 } : { rotateX: 0 }}
                    transition={{ duration: 0.3, ease: [0.16, 1, 0.3, 1] }}
                >
                    <FolderCoverIcon />
                    <div
                        className={cn(
                            "absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2",
                            sizes.deco,
                            colors.deco
                        )}
                    >
                        <UsersIcon />
                    </div>
                </motion.div>
            </div>

            {label && (
                <h3 className={cn("mt-3 font-medium text-center", sizes.caption, colors.caption)}>
                    {label}
                </h3>
            )}
        </div>
    );
};


const ArdraFolder: React.FC<{
    images: string[];
    isHovered: boolean;
    colors: typeof variantColors.ardra;
    sizes: typeof sizeConfig.md;
    label?: string;
}> = ({ images, isHovered, colors, sizes, label }) => {
    const [randomPositions] = React.useState(() =>
        images.map((_, i) => {
            const radius = 60 + Math.random() * 20;
            const angle = (2 * (i + 1) * Math.PI) / images.length;
            return {
                x: Math.round(radius * Math.cos(angle)),
                y: Math.round(radius * Math.sin(angle)),
                rotate: Math.random() * 6 - 3,
            };
        })
    );

    return (
        <div className="relative">
            {/* Feedback Circle */}
            <motion.div
                className="absolute inset-0 flex items-center justify-center pointer-events-none"
                initial={{ opacity: 0 }}
                animate={isHovered ? { opacity: 1 } : { opacity: 0 }}
            >
                <motion.div
                    className="w-10 h-10 rounded-full bg-gray-900/50"
                    initial={{ scale: 1 }}
                    animate={
                        isHovered ? { opacity: [1, 0], scale: [1, 6] } : { opacity: 0, scale: 1 }
                    }
                    transition={{ duration: 0.9, ease: [0.1, 1, 0.3, 1] }}
                />
            </motion.div>

            {/* Previews - positioned from folder center */}
            <div className="absolute inset-0 flex items-center justify-center pointer-events-none z-10">
                {images.map((img, i) => (
                    <motion.img
                        key={i}
                        src={img}
                        alt=""
                        className="absolute w-10 h-10 object-cover rounded-full border-2 border-white shadow-lg"
                        initial={{ opacity: 0, scale: 0.4, x: 0, y: 0, rotate: 0 }}
                        animate={
                            isHovered
                                ? {
                                    opacity: 1,
                                    scale: 1,
                                    x: randomPositions[i]?.x || 0,
                                    y: randomPositions[i]?.y || 0,
                                    rotate: randomPositions[i]?.rotate || 0,
                                }
                                : { opacity: 0, scale: 0.4, x: 0, y: 0, rotate: 0 }
                        }
                        transition={{ duration: 0.5, ease: [0.1, 1, 0.3, 1] }}
                    />
                ))}
            </div>

            {/* Folder */}
            <motion.div
                className="relative cursor-pointer aspect-[20/16]"
                animate={isHovered ? { scale: 0.85 } : { scale: 1 }}
                transition={{ duration: 0.5, ease: [0.1, 1, 0.3, 1] }}
            >
                {/* Back */}
                <div className={cn("absolute inset-0", colors.back)}>
                    <FolderBackIcon />
                </div>

                {/* Cover */}
                <div className={cn("relative", colors.cover)}>
                    <FolderCoverIcon />
                    <div
                        className={cn(
                            "absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2",
                            sizes.deco,
                            colors.deco
                        )}
                    >
                        <GlobeIcon />
                    </div>
                </div>
            </motion.div>

            {label && (
                <h3 className={cn("mt-3 font-medium text-center", sizes.caption, colors.caption)}>
                    {label}
                </h3>
            )}
        </div>
    );
};

const ShaktiFolder: React.FC<{
    images: string[];
    isHovered: boolean;
    colors: typeof variantColors.shakti;
    sizes: typeof sizeConfig.md;
    label?: string;
}> = ({ images, isHovered, colors, sizes, label }) => {
    return (
        <motion.div
            className="relative"
            animate={isHovered ? { y: 15 } : { y: 0 }}
            transition={{ duration: 0.4, ease: [0.2, 1, 0.3, 1] }}
        >
            <div className="relative cursor-pointer">
                {/* Back */}
                <div className={cn("absolute inset-0", colors.back)}>
                    <FolderBackIcon />
                </div>

                {/* Previews - Fan animation */}
                <div className={cn("absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2", sizes.thumb)}>
                    {images.map((img, i) => (
                        <motion.img
                            key={i}
                            src={img}
                            alt=""
                            className="absolute w-12 h-16 object-cover rounded shadow-lg origin-[-600%_50%]"
                            initial={{ opacity: 0, rotate: 0 }}
                            animate={
                                isHovered
                                    ? { opacity: 1, rotate: -10 * (images.length - i - 1) - 15 }
                                    : { opacity: 0, rotate: 0 }
                            }
                            transition={{
                                duration: 0.5,
                                delay: i * 0.08,
                                ease: [0.1, 1, 0.3, 1],
                            }}
                        />
                    ))}
                </div>

                {/* Cover */}
                <div className={cn("relative", colors.cover)}>
                    <FolderCoverIcon />
                    <div
                        className={cn(
                            "absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 mt-1",
                            sizes.deco,
                            colors.deco
                        )}
                    >
                        <PadlockIcon />
                    </div>
                </div>
            </div>

            {label && (
                <h3 className={cn("mt-3 font-medium text-center", sizes.caption, colors.caption)}>
                    {label}
                </h3>
            )}
        </motion.div>
    );
};

const KuberaFolder: React.FC<{
    images: string[];
    isHovered: boolean;
    colors: typeof variantColors.kubera;
    sizes: typeof sizeConfig.md;
    label?: string;
}> = ({ images, isHovered, colors, sizes, label }) => {
    const floatingMotions = React.useMemo(
        () =>
            images.map((_, i) => ({
                y: -200 - ((i * 17) % 50),
                x: ((i * 29) % 50) - 25,
                rotate: ((i * 19) % 40) - 20,
            })),
        [images]
    );

    return (
        <div className="relative">
            <div className="relative cursor-pointer" style={{ perspective: "800px" }}>
                {/* Back */}
                <div className={cn("absolute inset-0", colors.back)}>
                    <FolderBackIcon />
                </div>

                {/* Paper Sheet Deco */}
                <div className="absolute bottom-0.5 left-0.5 right-0.5 h-3/4 bg-white dark:bg-gray-200 rounded-lg" />

                {/* Floating Previews */}
                <div className={cn("absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2", sizes.thumb)}>
                    {images.map((img, i) => (
                        <motion.img
                            key={i}
                            src={img}
                            alt=""
                            className="absolute w-full h-full object-cover rounded-lg shadow-lg"
                            initial={{ opacity: 0 }}
                            animate={
                                isHovered
                                    ? {
                                        opacity: [1, 0],
                                        y: [0, floatingMotions[i]?.y ?? -200],
                                        x: floatingMotions[i]?.x ?? 0,
                                        rotate: floatingMotions[i]?.rotate ?? 0,
                                    }
                                    : { opacity: 0 }
                            }
                            transition={{
                                duration: 0.4,
                                delay: i * 0.3,
                                repeat: isHovered ? Infinity : 0,
                                ease: "linear",
                            }}
                        />
                    ))}
                </div>

                {/* Cover */}
                <motion.div
                    className={cn("relative", colors.cover)}
                    style={{ transformOrigin: "50% 100%", transformStyle: "preserve-3d" }}
                    animate={isHovered ? { rotateX: -40 } : { rotateX: 0 }}
                    transition={{ duration: 0.4, ease: [0.16, 1, 0.3, 1] }}
                >
                    <FolderCoverIcon />
                    <div
                        className={cn(
                            "absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 mt-1",
                            sizes.deco,
                            colors.deco
                        )}
                    >
                        <CloudIcon />
                    </div>
                </motion.div>
            </div>

            {label && (
                <h3 className={cn("mt-3 font-medium text-center", sizes.caption, colors.caption)}>
                    {label}
                </h3>
            )}
        </div>
    );
};

const HariFolder: React.FC<{
    images: string[];
    isHovered: boolean;
    colors: typeof variantColors.hari;
    sizes: typeof sizeConfig.md;
    label?: string;
}> = ({ images, isHovered, colors, sizes, label }) => {
    const positions = createCircularPositions(images.length, 120);

    return (
        <div className="relative">
            {/* Feedback Circle */}
            <motion.div
                className="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-14 h-14 rounded-full bg-sky-500"
                initial={{ opacity: 0, scale: 1 }}
                animate={
                    isHovered ? { opacity: [1, 0], scale: [1, 15] } : { opacity: 0, scale: 1 }
                }
                transition={{ duration: 1.1, delay: 0.2, ease: [0.1, 1, 0.3, 1] }}
            />

            {/* Jumping Previews */}
            <div className={cn("absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2", sizes.thumb)}>
                {images.map((img, i) => (
                    <motion.img
                        key={i}
                        src={img}
                        alt=""
                        className="absolute w-12 h-16 object-cover rounded shadow-lg"
                        initial={{ opacity: 0, scale: 0.5, x: 0, y: 0 }}
                        animate={
                            isHovered
                                ? {
                                    opacity: 1,
                                    scale: 1,
                                    x: -positions[i]?.x || 0,
                                    y: -positions[i]?.y || 0,
                                }
                                : { opacity: 0, scale: 0.5, x: 0, y: 0 }
                        }
                        transition={{
                            duration: 0.8,
                            delay: 0.2,
                            type: "spring",
                            stiffness: 200,
                            damping: 15,
                        }}
                    />
                ))}
            </div>

            <motion.div
                className="relative cursor-pointer"
                style={{ perspective: "800px", transformOrigin: "50% 100%" }}
                animate={
                    isHovered
                        ? { y: -20, scaleX: 0.9, scaleY: 0.9 }
                        : { y: 0, scaleX: 1, scaleY: 1 }
                }
                transition={{
                    duration: 0.8,
                    type: "spring",
                    stiffness: 200,
                    damping: 15,
                }}
            >
                {/* Back */}
                <div className={cn("absolute inset-0", colors.back)}>
                    <FolderBackIcon />
                </div>

                {/* Paper Sheet Deco */}
                <div className="absolute bottom-0.5 left-0.5 right-0.5 h-3/4 bg-white/80 rounded-lg" />

                {/* Cover */}
                <motion.div
                    className={cn("relative", colors.cover)}
                    style={{ transformOrigin: "50% 100%", transformStyle: "preserve-3d" }}
                    animate={isHovered ? { rotateX: -25 } : { rotateX: 0 }}
                    transition={{ duration: 0.4, delay: 0.2, ease: [0.16, 1, 0.3, 1] }}
                >
                    <FolderCoverIcon />
                    <div
                        className={cn(
                            "absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 mt-1",
                            sizes.deco,
                            colors.deco
                        )}
                    >
                        <GlobeIcon />
                    </div>
                </motion.div>
            </motion.div>

            <motion.h3
                className={cn("mt-3 font-medium text-center", sizes.caption, colors.caption)}
                animate={isHovered ? { opacity: 0 } : { opacity: 1 }}
                transition={{ delay: isHovered ? 0.3 : 0 }}
            >
                {label}
            </motion.h3>
        </div>
    );
};

const RaviFolder: React.FC<{
    images: string[];
    isHovered: boolean;
    colors: typeof variantColors.ravi;
    sizes: typeof sizeConfig.md;
    label?: string;
}> = ({ images, isHovered, colors, sizes, label }) => {
    // Reorder images for card-spread effect
    const reorder = (arr: string[]) => {
        const result: string[] = [];
        let i = Math.ceil(arr.length / 2);
        let j = i - 1;
        while (j >= 0) {
            result.push(arr[j--]);
            if (i < arr.length) result.push(arr[i++]);
        }
        return result;
    };

    const orderedImages = React.useMemo(() => reorder(images), [images]);

    return (
        <div className="relative">
            {/* Feedback Circle */}
            <motion.div
                className="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-14 h-14 rounded-full bg-white"
                initial={{ opacity: 0, scale: 1 }}
                animate={
                    !isHovered ? { opacity: [1, 0], scale: [1, 5] } : { opacity: 0, scale: 1 }
                }
                transition={{ duration: 0.8, delay: 0.35, ease: [0.1, 1, 0.3, 1] }}
            />

            <div className="relative cursor-pointer" style={{ perspective: "800px" }}>
                {/* Back */}
                <div className={cn("absolute inset-0", colors.back)}>
                    <FolderBackIcon />
                </div>

                {/* Paper Sheet */}
                <div className="absolute bottom-0.5 left-0.5 right-0.5 h-3/4 bg-white rounded-lg" />

                {/* Card Spread Previews */}
                <div className="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-[75px] h-[65px]">
                    {orderedImages.map((img, i) => {
                        const interval = 60;
                        const c = orderedImages.length;
                        const x =
                            -interval * Math.floor(c / 2) + interval * i + (c % 2 === 0 ? interval / 2 : 0);
                        const rotateInterval = 20;
                        const rotate =
                            -rotateInterval * Math.floor(c / 2) +
                            rotateInterval * i +
                            (c % 2 === 0 ? rotateInterval / 2 : 0);

                        return (
                            <motion.img
                                key={i}
                                src={img}
                                alt=""
                                className="absolute w-full h-full object-cover rounded shadow-lg"
                                initial={{ opacity: 0, x: 0, y: 0, rotate: 0, scale: 1 }}
                                animate={
                                    isHovered
                                        ? { opacity: 1, y: -70, x, rotate }
                                        : { opacity: 0, x: 0, y: 0, rotate: 0, scale: 0.5 }
                                }
                                transition={{
                                    duration: isHovered ? 0.4 : 0.3,
                                    ease: isHovered ? [0.1, 1, 0.3, 1] : "easeInOut",
                                }}
                            />
                        );
                    })}
                </div>

                {/* Cover */}
                <motion.div
                    className={cn("relative", colors.cover)}
                    style={{ transformOrigin: "50% 100%", transformStyle: "preserve-3d" }}
                    animate={isHovered ? { rotateX: -30 } : { rotateX: 0 }}
                    transition={{
                        duration: 0.4,
                        delay: isHovered ? 0 : 0.3,
                        ease: [0.16, 1, 0.3, 1],
                    }}
                >
                    <FolderCoverIcon />
                    <div
                        className={cn(
                            "absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 mt-1",
                            sizes.deco,
                            colors.deco
                        )}
                    >
                        <PadlockIcon />
                    </div>
                </motion.div>
            </div>

            {label && (
                <h3 className={cn("mt-3 font-medium text-center", sizes.caption, colors.caption)}>
                    {label}
                </h3>
            )}
        </div>
    );
};

const DurgaFolder: React.FC<{
    files: { name: string; type?: string }[];
    isHovered: boolean;
    colors: typeof variantColors.durga;
    sizes: typeof sizeConfig.md;
    label?: string;
}> = ({ files, isHovered, colors, sizes, label }) => {
    return (
        <div className="relative">
            {/* Text Preview - shows file list inside a tooltip bubble */}
            <motion.div
                className="absolute -right-2 top-0 bg-gray-800 dark:bg-gray-900 rounded-lg px-3 py-2 shadow-lg z-20 min-w-[100px]"
                initial={{ opacity: 0, x: -10, scale: 0.9 }}
                animate={isHovered ? { opacity: 1, x: 0, scale: 1 } : { opacity: 0, x: -10, scale: 0.9 }}
                transition={{ duration: 0.3, ease: [0.16, 1, 0.3, 1] }}
                style={{ transform: 'translateX(100%)' }}
            >
                {files.slice(0, 6).map((file, i) => (
                    <motion.div
                        key={i}
                        className="text-gray-100 font-mono text-xs py-0.5 whitespace-nowrap"
                        initial={{ opacity: 0 }}
                        animate={{ opacity: isHovered ? 1 : 0 }}
                        transition={{ duration: 0.05, delay: i * 0.03 }}
                    >
                        {file.name}
                    </motion.div>
                ))}
            </motion.div>

            {/* Folder */}
            <div className="relative cursor-pointer aspect-[20/16]" style={{ perspective: "800px" }}>
                {/* Back */}
                <div className={cn("absolute inset-0", colors.back)}>
                    <FolderBackIcon />
                </div>

                {/* Paper Sheet */}
                <div className="absolute bottom-0.5 left-0.5 right-0.5 h-3/4 bg-white dark:bg-gray-200 rounded-lg" />

                {/* Cover */}
                <motion.div
                    className={cn("relative", colors.cover)}
                    style={{ transformOrigin: "50% 100%", transformStyle: "preserve-3d" }}
                    animate={isHovered ? { rotateX: -30 } : { rotateX: 0 }}
                    transition={{ duration: 0.3, ease: [0.16, 1, 0.3, 1] }}
                >
                    <FolderCoverIcon />
                    <div
                        className={cn(
                            "absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2",
                            sizes.deco,
                            colors.deco
                        )}
                    >
                        <GlobeIcon />
                    </div>
                </motion.div>
            </div>

            {label && (
                <h3 className={cn("mt-3 font-medium text-center", sizes.caption, colors.caption)}>
                    {label}
                </h3>
            )}
        </div>
    );
};

const NandiFolder: React.FC<{
    files: { name: string; type?: string }[];
    isHovered: boolean;
    colors: typeof variantColors.nandi;
    sizes: typeof sizeConfig.md;
    label?: string;
}> = ({ files, isHovered, colors, sizes, label }) => {
    const fileColorMap: Record<string, string> = {
        txt: "fill-blue-300",
        gif: "fill-teal-400",
        mp3: "fill-amber-400",
        default: "fill-gray-400",
    };

    return (
        <div className="relative">
            {/* Magnifier Preview - compact bubble above folder */}
            <motion.div
                className="absolute left-1/2 -translate-x-1/2 bg-white dark:bg-gray-100 rounded-2xl shadow-xl z-20 p-3 grid grid-cols-3 gap-2"
                style={{ bottom: '100%', marginBottom: '8px', minWidth: '120px' }}
                initial={{ opacity: 0, scale: 0.8, y: 10 }}
                animate={
                    isHovered
                        ? { opacity: 1, scale: 1, y: 0 }
                        : { opacity: 0, scale: 0.8, y: 10 }
                }
                transition={{ duration: 0.3, ease: [0.16, 1, 0.3, 1] }}
            >
                {files.slice(0, 6).map((file, i) => (
                    <div key={i} className="text-center">
                        <FileIcon
                            className={cn("w-5 h-5 mx-auto", fileColorMap[file.type || "default"])}
                        />
                        <span className="text-[8px] text-gray-600 block mt-0.5 truncate max-w-[35px]">
                            {file.name}
                        </span>
                    </div>
                ))}
            </motion.div>

            {/* Folder */}
            <div className="relative cursor-pointer aspect-[20/16]" style={{ perspective: "800px" }}>
                {/* Back */}
                <div className={cn("absolute inset-0", colors.back)}>
                    <FolderBackIcon />
                </div>

                {/* Paper Sheet */}
                <div className="absolute bottom-0.5 left-0.5 right-0.5 h-3/4 bg-white dark:bg-gray-200 rounded-lg" />

                {/* Cover */}
                <motion.div
                    className={cn("relative", colors.cover)}
                    style={{ transformOrigin: "50% 100%", transformStyle: "preserve-3d" }}
                    animate={isHovered ? { rotateX: -30 } : { rotateX: 0 }}
                    transition={{ duration: 0.3, ease: [0.16, 1, 0.3, 1] }}
                >
                    <FolderCoverIcon />
                    <div
                        className={cn(
                            "absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2",
                            sizes.deco,
                            colors.deco
                        )}
                    >
                        <CloudIcon />
                    </div>
                </motion.div>
            </div>

            {label && (
                <h3 className={cn("mt-3 font-medium text-center", sizes.caption, colors.caption)}>
                    {label}
                </h3>
            )}
        </div>
    );
};

// ============================================
// Main FolderPreview Component
// ============================================

export const FolderPreview = React.forwardRef<HTMLDivElement, FolderPreviewProps>(
    (
        {
            variant = "devi",
            images = [],
            files = [],
            label,
            size = "md",
            className,
            onClick,
        },
        ref
    ) => {
        const [isHovered, setIsHovered] = React.useState(false);
        const colors = variantColors[variant];
        const sizes = sizeConfig[size];

        const defaultImages = [
            "/folder-preview/user1.svg",
            "/folder-preview/user2.svg",
            "/folder-preview/user3.svg",
            "/folder-preview/user4.svg",
            "/folder-preview/user5.svg",
        ];

        const defaultFiles = [
            { name: "docs", type: "default" as const },
            { name: "template", type: "default" as const },
            { name: "readme.md", type: "txt" as const },
            { name: "app.js", type: "txt" as const },
            { name: "test.sh", type: "txt" as const },
            { name: "package.json", type: "txt" as const },
            { name: "logo.svg", type: "default" as const },
            { name: "...", type: "default" as const },
        ];

        const imageList = images.length > 0 ? images : defaultImages;
        const fileList = files.length > 0 ? files : defaultFiles;

        const renderFolder = () => {
            const props = { isHovered, colors, sizes, label };

            switch (variant) {
                case "devi":
                    return <DeviFolder images={imageList} {...props} />;
                case "rudras":
                    return <RudrasFolder images={imageList} {...props} />;
                case "ardra":
                    return <ArdraFolder images={imageList} {...props} />;
                case "shakti":
                    return <ShaktiFolder images={imageList} {...props} />;
                case "kubera":
                    return <KuberaFolder images={imageList} {...props} />;
                case "hari":
                    return <HariFolder images={imageList} {...props} />;
                case "ravi":
                    return <RaviFolder images={imageList} {...props} />;
                case "durga":
                    return <DurgaFolder files={fileList} {...props} />;
                case "nandi":
                    return <NandiFolder files={fileList} {...props} />;
                default:
                    return <DeviFolder images={imageList} {...props} />;
            }
        };

        return (
            <div
                ref={ref}
                className={cn("inline-flex flex-col items-center overflow-visible", sizes.folder, className)}
                onMouseEnter={() => setIsHovered(true)}
                onMouseLeave={() => setIsHovered(false)}
                onClick={onClick}
            >
                {renderFolder()}
            </div>
        );
    }
);

FolderPreview.displayName = "FolderPreview";

export default FolderPreview;

```

---

## `src\components\ui\footer.tsx`

```tsx
import React from 'react';

const Footer: React.FC = () => {
  return (
    <footer className="relative bg-black text-white w-full min-h-[600px] flex flex-col overflow-hidden pt-20 px-6 md:px-12 lg:px-24">

      <article id="lighting-wrap">
        <article id="lightings">
          <section id="light-one" className="lighting-section">
            <section id="light-two" className="lighting-section">
              <section id="light-three" className="lighting-section">
                <section id="light-four" className="lighting-section">
                  <section id="light-five" className="lighting-section" />
                </section>
              </section>
            </section>
          </section>
        </article>
      </article>

      <div className="flex flex-col lg:flex-row justify-between w-full h-full pb-20 z-10 relative">

        <div className="flex flex-col mb-32 lg:mb-28 max-w-xl">

          <div className="mb-8">
            <span className="inline-block px-4 py-1.5 rounded-full bg-zinc-900 text-zinc-400 text-[10px] font-medium tracking-[0.2em] uppercase">
              Vengeance UI
            </span>
          </div>

          <h2 className="text-3xl md:text-5xl lg:text-6xl font-serif-elegant leading-tight">
            Build landing pages<br />
            that feel alive<br />
            and effortless
          </h2>

          <p className="mt-6 text-zinc-400 text-sm leading-relaxed max-w-md">
            VengeanceUI helps you build stunning landing pages using beautifully
            animated, copy-paste ready components — crafted to be subtle,
            tasteful, and production-ready.
          </p>
        </div>

        <div className="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-12 lg:gap-24">

          <div className="flex flex-col space-y-6">
            <h3 className="text-zinc-500 text-[11px] font-medium tracking-[0.2em] uppercase">
              Product
            </h3>
            <ul className="flex flex-col space-y-3">
              <li><FooterLink href="#">Components</FooterLink></li>
              <li><FooterLink href="#">Animations</FooterLink></li>
              <li><FooterLink href="#">Templates</FooterLink></li>
              <li><FooterLink href="#">Showcase</FooterLink></li>
            </ul>
          </div>

          <div className="flex flex-col space-y-6">
            <h3 className="text-zinc-500 text-[11px] font-medium tracking-[0.2em] uppercase">
              Resources
            </h3>
            <ul className="flex flex-col space-y-3">
              <li><FooterLink href="#">Documentation</FooterLink></li>
              <li><FooterLink href="#">Getting Started</FooterLink></li>
              <li><FooterLink href="#">Changelog</FooterLink></li>
              <li><FooterLink href="#">Roadmap</FooterLink></li>
            </ul>
          </div>

          <div className="flex flex-col space-y-6">
            <h3 className="text-zinc-500 text-[11px] font-medium tracking-[0.2em] uppercase">
              Community
            </h3>
            <ul className="flex flex-col space-y-3">
              <li><FooterLink href="#">GitHub</FooterLink></li>
              <li><FooterLink href="#">Discord</FooterLink></li>
              <li><FooterLink href="#">Twitter / X</FooterLink></li>
              <li><FooterLink href="#">Instagram</FooterLink></li>
            </ul>
          </div>
        </div>
      </div>

      <div className="absolute bottom-0 left-0 w-full pointer-events-none select-none flex justify-center overflow-hidden">
        <h1 className="text-[18vw] font-serif-elegant leading-none translate-y-[0%] tracking-tighter whitespace-nowrap wordmark-gradient">
          Vengeance UI
        </h1>
      </div>

      <div className="mt-auto pb-8 z-10 text-center w-full relative">
        <p className="text-[10px] tracking-[0.2em] text-zinc-700 font-medium uppercase">
          © 2025 Vengeance UI — Crafted for modern web builders
        </p>
      </div>
    </footer>
  );
};

interface FooterLinkProps {
  href: string;
  children: React.ReactNode;
}

const FooterLink: React.FC<FooterLinkProps> = ({ href, children }) => {
  return (
    <a
      href={href}
      className="text-zinc-200 hover:text-white text-sm tracking-wide transition-colors duration-200 ease-in-out block"
    >
      {children}
    </a>
  );
};

export default Footer;
```

---

## `src\components\ui\form.tsx`

```tsx
"use client"

import * as React from "react"
import * as LabelPrimitive from "@radix-ui/react-label"
import { Slot } from "@radix-ui/react-slot"
import {
  Controller,
  FormProvider,
  useFormContext,
  useFormState,
  type ControllerProps,
  type FieldPath,
  type FieldValues,
} from "react-hook-form"

import { cn } from "@/lib/utils"
import { Label } from "@/components/ui/label"

const Form = FormProvider

type FormFieldContextValue<
  TFieldValues extends FieldValues = FieldValues,
  TName extends FieldPath<TFieldValues> = FieldPath<TFieldValues>,
> = {
  name: TName
}

const FormFieldContext = React.createContext<FormFieldContextValue>(
  {} as FormFieldContextValue
)

const FormField = <
  TFieldValues extends FieldValues = FieldValues,
  TName extends FieldPath<TFieldValues> = FieldPath<TFieldValues>,
>({
  ...props
}: ControllerProps<TFieldValues, TName>) => {
  return (
    <FormFieldContext.Provider value={{ name: props.name }}>
      <Controller {...props} />
    </FormFieldContext.Provider>
  )
}

const useFormField = () => {
  const fieldContext = React.useContext(FormFieldContext)
  const itemContext = React.useContext(FormItemContext)
  const { getFieldState } = useFormContext()
  const formState = useFormState({ name: fieldContext.name })
  const fieldState = getFieldState(fieldContext.name, formState)

  if (!fieldContext) {
    throw new Error("useFormField should be used within <FormField>")
  }

  const { id } = itemContext

  return {
    id,
    name: fieldContext.name,
    formItemId: `${id}-form-item`,
    formDescriptionId: `${id}-form-item-description`,
    formMessageId: `${id}-form-item-message`,
    ...fieldState,
  }
}

type FormItemContextValue = {
  id: string
}

const FormItemContext = React.createContext<FormItemContextValue>(
  {} as FormItemContextValue
)

function FormItem({ className, ...props }: React.ComponentProps<"div">) {
  const id = React.useId()

  return (
    <FormItemContext.Provider value={{ id }}>
      <div
        data-slot="form-item"
        className={cn("grid gap-2", className)}
        {...props}
      />
    </FormItemContext.Provider>
  )
}

function FormLabel({
  className,
  ...props
}: React.ComponentProps<typeof LabelPrimitive.Root>) {
  const { error, formItemId } = useFormField()

  return (
    <Label
      data-slot="form-label"
      data-error={!!error}
      className={cn("data-[error=true]:text-destructive", className)}
      htmlFor={formItemId}
      {...props}
    />
  )
}

function FormControl({ ...props }: React.ComponentProps<typeof Slot>) {
  const { error, formItemId, formDescriptionId, formMessageId } = useFormField()

  return (
    <Slot
      data-slot="form-control"
      id={formItemId}
      aria-describedby={
        !error
          ? `${formDescriptionId}`
          : `${formDescriptionId} ${formMessageId}`
      }
      aria-invalid={!!error}
      {...props}
    />
  )
}

function FormDescription({ className, ...props }: React.ComponentProps<"p">) {
  const { formDescriptionId } = useFormField()

  return (
    <p
      data-slot="form-description"
      id={formDescriptionId}
      className={cn("text-muted-foreground text-sm", className)}
      {...props}
    />
  )
}

function FormMessage({ className, ...props }: React.ComponentProps<"p">) {
  const { error, formMessageId } = useFormField()
  const body = error ? String(error?.message ?? "") : props.children

  if (!body) {
    return null
  }

  return (
    <p
      data-slot="form-message"
      id={formMessageId}
      className={cn("text-destructive text-sm", className)}
      {...props}
    >
      {body}
    </p>
  )
}

export {
  useFormField,
  Form,
  FormItem,
  FormLabel,
  FormControl,
  FormDescription,
  FormMessage,
  FormField,
}

```

---

## `src\components\ui\generate-button.tsx`

```tsx
"use client";

import React, { useState, useEffect } from "react";
import { cn } from "@/lib/utils";

export interface GenerateButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  /** 
   * The hue value (0-360) for the button's highlight color.
   * Default is 210 (Blue).
   */
  hue?: number;
  /**
   * If true, forces the button into its "Generating" state.
   * By default, the button also enters this state when focused or clicked.
   */
  isGenerating?: boolean;
}

export function GenerateButton({
  hue = 210,
  isGenerating: controlledIsGenerating,
  className,
  onClick,
  ...props
}: GenerateButtonProps) {
  const [isFocused, setIsFocused] = useState(false);
  
  const isGenerating = controlledIsGenerating !== undefined ? controlledIsGenerating : isFocused;

  return (
    <div className="relative inline-block group">
      <style>{`
        .gen-btn {
          --border-radius: 24px;
          --padding: 4px;
          --transition: 0.4s;
          --button-color: #101010;
          --highlight-color-hue: ${hue}deg;

          user-select: none;
          display: flex;
          justify-content: center;
          padding: 0.5em 0.5em 0.5em 1.1em;
          font-family: "Poppins", "Inter", "Segoe UI", sans-serif;
          font-size: 1em;
          font-weight: 400;

          background-color: var(--button-color);

          box-shadow:
            inset 0px 1px 1px rgba(255, 255, 255, 0.2),
            inset 0px 2px 2px rgba(255, 255, 255, 0.15),
            inset 0px 4px 4px rgba(255, 255, 255, 0.1),
            inset 0px 8px 8px rgba(255, 255, 255, 0.05),
            inset 0px 16px 16px rgba(255, 255, 255, 0.05),
            0px -1px 1px rgba(0, 0, 0, 0.02),
            0px -2px 2px rgba(0, 0, 0, 0.03), 
            0px -4px 4px rgba(0, 0, 0, 0.05),
            0px -8px 8px rgba(0, 0, 0, 0.06), 
            0px -16px 16px rgba(0, 0, 0, 0.08);

          border: solid 1px rgba(255, 255, 255, 0.133);
          border-radius: var(--border-radius);
          cursor: pointer;

          transition: box-shadow var(--transition), border var(--transition), background-color var(--transition);
        }
        
        .gen-btn::before {
          content: "";
          position: absolute;
          top: calc(0px - var(--padding));
          left: calc(0px - var(--padding));
          width: calc(100% + var(--padding) * 2);
          height: calc(100% + var(--padding) * 2);
          border-radius: calc(var(--border-radius) + var(--padding));
          pointer-events: none;
          background-image: linear-gradient(0deg, rgba(0,0,0,0.267), rgba(0,0,0,0.667));

          z-index: -1;
          transition: box-shadow var(--transition), filter var(--transition);
          box-shadow: 0 -8px 8px -6px rgba(0,0,0,0) inset, 
            0 -16px 16px -8px rgba(0,0,0,0) inset,
            1px 1px 1px rgba(255,255,255,0.133), 
            2px 2px 2px rgba(255,255,255,0.067), 
            -1px -1px 1px rgba(0,0,0,0.133),
            -2px -2px 2px rgba(0,0,0,0.067);
        }
        
        .gen-btn::after {
          content: "";
          position: absolute;
          top: 0;
          left: 0;
          width: 100%;
          height: 100%;
          border-radius: inherit;
          pointer-events: none;
          background-image: linear-gradient(
            0deg,
            #fff,
            hsl(var(--highlight-color-hue), 100%, 70%),
            hsla(var(--highlight-color-hue), 100%, 70%, 50%),
            8%,
            transparent
          );
          background-position: 0 0;
          opacity: 0;
          transition: opacity var(--transition), filter var(--transition);
        }

        .gen-btn-letter {
          position: relative;
          display: inline-block;
          color: rgba(255,255,255,0.333);
          animation: gen-letter-anim 2s ease-in-out infinite;
          transition: color var(--transition), text-shadow var(--transition), opacity var(--transition);
        }

        @keyframes gen-letter-anim {
          50% {
            text-shadow: 0 0 3px rgba(255,255,255,0.533);
            color: #fff;
          }
        }

        .gen-btn-svg {
          flex-grow: 1;
          height: 24px;
          margin-right: 0.5rem;
          fill: #e8e8e8;
          animation: gen-flicker 2s linear infinite;
          animation-delay: 0.5s;
          filter: drop-shadow(0 0 2px rgba(255,255,255,0.6));
          transition: fill var(--transition), filter var(--transition), opacity var(--transition);
        }
        
        @keyframes gen-flicker {
          50% { opacity: 0.3; }
        }

        .gen-txt-wrapper {
          position: relative;
          display: flex;
          align-items: center;
          min-width: 6.4em;
        }
        
        .gen-txt-1,
        .gen-txt-2 {
          position: absolute;
          word-spacing: -1em;
        }
        
        .gen-txt-1 {
          animation: gen-appear-anim 1s ease-in-out forwards;
        }
        
        .gen-txt-2 {
          opacity: 0;
        }
        
        @keyframes gen-appear-anim {
          0% { opacity: 0; }
          100% { opacity: 1; }
        }
        
        /* Generating (Focus/Active) state */
        .gen-btn[data-generating="true"] .gen-txt-1 {
          animation: gen-opacity-anim 0.3s ease-in-out forwards;
          animation-delay: 1s;
        }
        .gen-btn[data-generating="true"] .gen-txt-2 {
          animation: gen-opacity-anim 0.3s ease-in-out reverse forwards;
          animation-delay: 1s;
        }
        
        @keyframes gen-opacity-anim {
          0% { opacity: 1; }
          100% { opacity: 0; }
        }

        .gen-btn[data-generating="true"] .gen-btn-letter {
          animation: gen-focused-letter-anim 1s ease-in-out forwards, gen-letter-anim 1.2s ease-in-out infinite;
          animation-delay: 0s, 1s;
        }
        
        @keyframes gen-focused-letter-anim {
          0%, 100% { filter: blur(0px); }
          50% {
            transform: scale(2);
            filter: blur(10px) brightness(150%) drop-shadow(-36px 12px 12px hsl(var(--highlight-color-hue), 100%, 70%));
          }
        }
        
        .gen-btn[data-generating="true"] .gen-btn-svg {
          animation-duration: 1.2s;
          animation-delay: 0.2s;
        }

        .gen-btn[data-generating="true"]::before {
          box-shadow: 0 -8px 12px -6px rgba(255,255,255,0.2) inset,
            0 -16px 16px -8px hsla(var(--highlight-color-hue), 100%, 70%, 20%) inset,
            1px 1px 1px rgba(255,255,255,0.2), 
            2px 2px 2px rgba(255,255,255,0.067), 
            -1px -1px 1px rgba(0,0,0,0.133),
            -2px -2px 2px rgba(0,0,0,0.067);
        }
        
        .gen-btn[data-generating="true"]::after {
          opacity: 0.6;
          mask-image: linear-gradient(0deg, #fff, transparent);
          filter: brightness(100%);
        }

        /* Animation delays for letters */
        .gen-btn-letter:nth-child(1), .gen-btn[data-generating="true"] .gen-btn-letter:nth-child(1) { animation-delay: 0s; }
        .gen-btn-letter:nth-child(2), .gen-btn[data-generating="true"] .gen-btn-letter:nth-child(2) { animation-delay: 0.08s; }
        .gen-btn-letter:nth-child(3), .gen-btn[data-generating="true"] .gen-btn-letter:nth-child(3) { animation-delay: 0.16s; }
        .gen-btn-letter:nth-child(4), .gen-btn[data-generating="true"] .gen-btn-letter:nth-child(4) { animation-delay: 0.24s; }
        .gen-btn-letter:nth-child(5), .gen-btn[data-generating="true"] .gen-btn-letter:nth-child(5) { animation-delay: 0.32s; }
        .gen-btn-letter:nth-child(6), .gen-btn[data-generating="true"] .gen-btn-letter:nth-child(6) { animation-delay: 0.4s; }
        .gen-btn-letter:nth-child(7), .gen-btn[data-generating="true"] .gen-btn-letter:nth-child(7) { animation-delay: 0.48s; }
        .gen-btn-letter:nth-child(8), .gen-btn[data-generating="true"] .gen-btn-letter:nth-child(8) { animation-delay: 0.56s; }
        .gen-btn-letter:nth-child(9), .gen-btn[data-generating="true"] .gen-btn-letter:nth-child(9) { animation-delay: 0.64s; }
        .gen-btn-letter:nth-child(10), .gen-btn[data-generating="true"] .gen-btn-letter:nth-child(10) { animation-delay: 0.72s; }
        .gen-btn-letter:nth-child(11), .gen-btn[data-generating="true"] .gen-btn-letter:nth-child(11) { animation-delay: 0.8s; }
        .gen-btn-letter:nth-child(12), .gen-btn[data-generating="true"] .gen-btn-letter:nth-child(12) { animation-delay: 0.88s; }
        .gen-btn-letter:nth-child(13), .gen-btn[data-generating="true"] .gen-btn-letter:nth-child(13) { animation-delay: 0.96s; }

        /* Hover & Active states */
        .gen-btn:active {
          border: solid 1px hsla(var(--highlight-color-hue), 100%, 80%, 70%);
          background-color: hsla(var(--highlight-color-hue), 50%, 20%, 0.5);
        }
        .gen-btn:active::before {
          box-shadow: 0 -8px 12px -6px rgba(255,255,255,0.667) inset,
            0 -16px 16px -8px hsla(var(--highlight-color-hue), 100%, 70%, 80%) inset,
            1px 1px 1px rgba(255,255,255,0.267), 
            2px 2px 2px rgba(255,255,255,0.133), 
            -1px -1px 1px rgba(0,0,0,0.133),
            -2px -2px 2px rgba(0,0,0,0.067);
        }
        .gen-btn:active::after {
          opacity: 1;
          mask-image: linear-gradient(0deg, #fff, transparent);
          filter: brightness(200%);
        }
        .gen-btn:active .gen-btn-letter {
          text-shadow: 0 0 1px hsla(var(--highlight-color-hue), 100%, 90%, 90%);
          animation: none;
        }

        .gen-btn:hover {
          border: solid 1px hsla(var(--highlight-color-hue), 100%, 80%, 40%);
        }
        .gen-btn:hover::before {
          box-shadow: 0 -8px 8px -6px rgba(255,255,255,0.667) inset,
            0 -16px 16px -8px hsla(var(--highlight-color-hue), 100%, 70%, 30%) inset,
            1px 1px 1px rgba(255,255,255,0.133), 
            2px 2px 2px rgba(255,255,255,0.067), 
            -1px -1px 1px rgba(0,0,0,0.133),
            -2px -2px 2px rgba(0,0,0,0.067);
        }
        .gen-btn:hover::after {
          opacity: 1;
          mask-image: linear-gradient(0deg, #fff, transparent);
        }
        .gen-btn:hover .gen-btn-svg {
          fill: #fff;
          filter: drop-shadow(0 0 3px hsl(var(--highlight-color-hue), 100%, 70%)) drop-shadow(0 -4px 6px rgba(0,0,0,0.6));
          animation: none;
        }
      `}</style>

      <button
        type="button"
        className={cn("gen-btn", className)}
        data-generating={isGenerating}
        onFocus={() => setIsFocused(true)}
        onBlur={() => setIsFocused(false)}
        onClick={(e) => {
          setIsFocused(true);
          onClick?.(e);
        }}
        {...props}
      >
        <svg className="gen-btn-svg" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
          <path strokeLinecap="round" strokeLinejoin="round" d="M9.813 15.904 9 18.75l-.813-2.846a4.5 4.5 0 0 0-3.09-3.09L2.25 12l2.846-.813a4.5 4.5 0 0 0 3.09-3.09L9 5.25l.813 2.846a4.5 4.5 0 0 0 3.09 3.09L15.75 12l-2.846.813a4.5 4.5 0 0 0-3.09 3.09ZM18.259 8.715 18 9.75l-.259-1.035a3.375 3.375 0 0 0-2.455-2.456L14.25 6l1.036-.259a3.375 3.375 0 0 0 2.455-2.456L18 2.25l.259 1.035a3.375 3.375 0 0 0 2.456 2.456L21.75 6l-1.035.259a3.375 3.375 0 0 0-2.456 2.456ZM16.894 20.567 16.5 21.75l-.394-1.183a2.25 2.25 0 0 0-1.423-1.423L13.5 18.75l1.183-.394a2.25 2.25 0 0 0 1.423-1.423l.394-1.183.394 1.183a2.25 2.25 0 0 0 1.423 1.423l1.183.394-1.183.394a2.25 2.25 0 0 0-1.423 1.423Z"></path>
        </svg>

        <div className="gen-txt-wrapper">
          <div className="gen-txt-1">
            {"Generate".split("").map((letter, i) => (
              <span key={`t1-${i}`} className="gen-btn-letter">{letter}</span>
            ))}
          </div>
          <div className="gen-txt-2">
            {"Generating".split("").map((letter, i) => (
              <span key={`t2-${i}`} className="gen-btn-letter">{letter}</span>
            ))}
          </div>
        </div>
      </button>
    </div>
  );
}

export default GenerateButton;

```

---

## `src\components\ui\github-button.tsx`

```tsx
'use client';
import React, { useState } from 'react';
import { FaGithub } from 'react-icons/fa';
import { Liquid ,Colors} from './liquid-gradient';

const COLORS: Colors = {
  color1: '#FFFFFF',
  color2: '#1E10C5',
  color3: '#9089E2',
  color4: '#FCFCFE',
  color5: '#F9F9FD',
  color6: '#B2B8E7',
  color7: '#0E2DCB',
  color8: '#0017E9',
  color9: '#4743EF',
  color10: '#7D7BF4',
  color11: '#0B06FC',
  color12: '#C5C1EA',
  color13: '#1403DE',
  color14: '#B6BAF6',
  color15: '#C1BEEB',
  color16: '#290ECB',
  color17: '#3F4CC0',
};


export function GithubButton(){
   const [isHovered, setIsHovered] = useState(false);

  return (
    <div className='flex justify-center'>
      <a
        href='https://github.com/Ashutoshx7/VengeanceUI'
        target='_blank'
        className='relative inline-block w-8 h-[2em] mx-auto group dark:bg-black bg-white dark:border-white border-black border-2 rounded-lg'
      >
        <div className='absolute w-[112.81%] h-[128.57%] top-[8.57%] left-1/2 -translate-x-1/2 filter blur-[19px] opacity-70'>
          <span className='absolute inset-0 rounded-lg bg-foreground/5 filter blur-[2px]'></span>
          <div className='relative w-full h-full overflow-hidden rounded-lg'>
            <Liquid isHovered={isHovered} colors={COLORS} />
          </div>
        </div>
        <div className='absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-[40%] w-[92.23%] h-[112.85%] rounded-lg bg-[#010128] filter blur-[7.3px]'></div>
        <div className='relative w-full h-full overflow-hidden rounded-lg'>
          <span className='absolute inset-0 rounded-lg bg-[#d9d9d9]'></span>
          <span className='absolute inset-0 rounded-lg bg-black'></span>
          <Liquid isHovered={isHovered} colors={COLORS} />
          {[1, 2, 3, 4, 5].map((i) => (
            <span
              key={i}
              className={`absolute inset-0 rounded-lg border-solid border-[3px] border-gradient-to-b from-transparent to-white mix-blend-overlay filter ${
                i <= 2 ? 'blur-[3px]' : i === 3 ? 'blur-[5px]' : 'blur-xs'
              }`}
            ></span>
          ))}
          <span className='absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-[40%] w-[70.8%] h-[42.85%] rounded-lg filter blur-[15px] bg-[#006]'></span>
        </div>
        <button
          className='absolute inset-0 rounded-lg bg-transparent cursor-pointer'
          aria-label='Get Started'
          type='button'
          onMouseEnter={() => setIsHovered(true)}
          onMouseLeave={() => setIsHovered(false)}
        >
          <span className=' flex items-center justify-center px-2 gap-2 rounded-lg group-hover:text-yellow-400 text-white text-xl font-semibold tracking-wide whitespace-nowrap'>
            <FaGithub className='inline-block size-4 shrink-0' />
          </span>
        </button>
      </a>
    </div>
  );

}
```

---

## `src\components\ui\glass-dock.tsx`

```tsx
'use client';

import React, { useRef, useState, useEffect } from 'react';
import { motion, AnimatePresence } from 'framer-motion';
import { cn } from '@/lib/utils';
import gsap from "gsap";

type DockIcon = React.ComponentType<{ className?: string }>;

export interface DockItem {
    title: string;
    icon: DockIcon;
    onClick?: () => void;
    href?: string;
}

export interface GlassDockProps extends React.HTMLAttributes<HTMLDivElement> {
    items: DockItem[];
    dockClassName?: string;
}

// Attempt to register MorphSVGPlugin if available. 
if (typeof window !== "undefined") {
    try {
        // @ts-ignore
        import("gsap/MorphSVGPlugin").then((plugin) => {
            gsap.registerPlugin(plugin.MorphSVGPlugin);
        }).catch(e => {
            console.warn("GSAP MorphSVGPlugin not found. Morphing animations will be disabled.", e);
        });
    } catch (e) {
        console.warn("GSAP MorphSVGPlugin not found.", e);
    }
}

// Helper component for Morphing Icons
const MorphingIcon = ({ type, isActive, onClick, onMouseEnter }: { type: string, isActive: boolean, onClick: () => void, onMouseEnter?: () => void }) => {
    const buttonRef = useRef<HTMLButtonElement>(null);
    const pathRef = useRef<SVGPathElement>(null);

    // Animation functions integrated directly
    const animateHome = () => {
        if (!buttonRef.current || !pathRef.current) return;
        const button = buttonRef.current;
        const path = pathRef.current;

        gsap.to(button, {
            "--tab-bar-home-scale": 0.25,
            "--tab-bar-home-opacity": 0,
            duration: 0.1,
            onComplete: () => {
                gsap.to(path, {
                    keyframes: [
                        { morphSVG: "M12.6387 3.53796L15.1949 7.69178C15.7004 8.51322 15.7802 9.5276 15.4092 10.4179L11.3846 20.0769C11.2016 20.516 11.5243 21 12 21V21C12.4757 21 12.7984 20.516 12.6154 20.0769L8.5908 10.4179C8.21983 9.5276 8.29956 8.51322 8.80506 7.69178L11.3613 3.53796C11.6541 3.06206 12.3459 3.06206 12.6387 3.53796Z", duration: 0.1 },
                        { morphSVG: "M12.1483 3.46366L12.8548 8.05624C12.9493 8.67024 12.8508 9.29842 12.573 9.85405L8.08541 18.8292C7.58673 19.8265 8.31198 21 9.42705 21H14.5729C15.688 21 16.4133 19.8265 15.9146 18.8292L11.427 9.85405C11.1492 9.29842 11.0507 8.67024 11.1452 8.05624L11.8517 3.46366C11.8778 3.29407 12.1222 3.29407 12.1483 3.46366Z", duration: 0.09 },
                        {
                            morphSVG: "M21 18V10.5339C21 9.57062 20.5374 8.66591 19.7565 8.1019L13.7565 3.76856C12.7079 3.01128 11.2921 3.01128 10.2435 3.76856L4.24353 8.1019C3.46259 8.66591 3 9.57062 3 10.5339V18C3 19.6569 4.34315 21 6 21H18C19.6569 21 21 19.6569 21 18Z",
                            duration: 0.71, ease: "elastic.out(1, .9)",
                            onStart: () => {
                                gsap.to(button, { "--tab-bar-home-scale": 0.7, duration: 0.71, ease: "elastic.out(1, .9)" });
                                gsap.to(button, { "--tab-bar-home-opacity": 1, duration: 0.2 });
                            },
                        },
                    ],
                });
            },
        });
    };

    // ... (Simulate other animations similarly or simplify for brevity in this single file component)
    // For brevity, mapping only the structure. Full GSAP keyframes should be here as in previous file.
    // I will include all animations to ensure full functionality.

    const animateBlog = () => {
        if (!buttonRef.current || !pathRef.current) return;
        const button = buttonRef.current;
        const path = pathRef.current;

        // Morphing animation for Blog
        gsap.to(button, {
            "--tab-bar-blog-scale": 0.25,
            "--tab-bar-blog-opacity": 0,
            duration: 0.1,
            onComplete: () => {
                gsap.to(path, {
                    keyframes: [
                        { morphSVG: "M12 21C12 21 15.3954 18.8605 13.3637 16C12.0647 14.1711 9.51275 11.9823 9 10C8 6.134 10.134 3 12 3C13.866 3 16 6.134 15 10C14.4873 11.9823 11.9353 14.1711 10.6363 16C8.60464 18.8605 12 21 12 21Z", duration: 0.1 },
                        { morphSVG: "M12 21C12 21 14.0216 19.0215 14.3637 16C14.6026 13.8898 13.5128 11.9823 13 10C12 6.134 13.134 3 12 3C10.866 3 12 6.134 11 10C10.4873 11.9823 9.39736 13.8898 9.6363 16C9.97843 19.0215 12 21 12 21Z", duration: 0.05 },
                        {
                            morphSVG: "M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zm-5 14H7v-2h7v2zm3-4H7v-2h10v2zm0-4H7V7h10v2z",
                            duration: 0.7,
                            ease: "elastic.out(1, .9)",
                            onStart: () => {
                                gsap.to(button, { "--tab-bar-blog-scale": 0.7, duration: 0.7, ease: "elastic.out(1, .9)" });
                                gsap.to(button, { "--tab-bar-blog-opacity": 1, duration: 0.2 });
                            }
                        }
                    ]
                });
            }
        });
    };

    const animateMarker = () => {
        if (!buttonRef.current || !pathRef.current) return;
        const button = buttonRef.current;
        const path = pathRef.current;
        gsap.to(button, {
            "--tab-bar-marker-scale": 0.25, "--tab-bar-marker-opacity": 0, duration: 0.1, onComplete: () => {
                gsap.to(path, {
                    keyframes: [
                        { morphSVG: "M12 21C12 21 15.3954 18.8605 13.3637 16C12.0647 14.1711 9.51275 11.9823 9 10C8 6.134 10.134 3 12 3C13.866 3 16 6.134 15 10C14.4873 11.9823 11.9353 14.1711 10.6363 16C8.60464 18.8605 12 21 12 21Z", duration: 0.1 },
                        { morphSVG: "M12 21C12 21 14.0216 19.0215 14.3637 16C14.6026 13.8898 13.5128 11.9823 13 10C12 6.134 13.134 3 12 3C10.866 3 12 6.134 11 10C10.4873 11.9823 9.39736 13.8898 9.6363 16C9.97843 19.0215 12 21 12 21Z", duration: 0.05 },
                        {
                            morphSVG: "M12 21C12 21 14.6062 18.8589 16.64 16C17.941 14.1711 19 12.0475 19 10C19 6.134 15.87 3 12 3C8.13 3 5 6.134 5 10C5 12.0475 6.05896 14.1711 7.36 16C9.39381 18.8589 12 21 12 21Z",
                            duration: 0.75, ease: "elastic.out(1, .9)",
                            onStart: () => {
                                gsap.to(button, { "--tab-bar-marker-scale": 0.7, duration: 0.75, ease: "elastic.out(1, .9)" });
                                gsap.to(button, { "--tab-bar-marker-opacity": 1, duration: 0.2 });
                            }
                        }
                    ]
                });
            }
        });
    };

    const animateEmail = () => {
        if (!buttonRef.current || !pathRef.current) return;
        const button = buttonRef.current;
        const path = pathRef.current;
        gsap.to(button, {
            "--tab-bar-email-scale": 0.25, "--tab-bar-email-opacity": 0, duration: 0.1, onComplete: () => {
                gsap.to(path, {
                    keyframes: [
                        { morphSVG: "M12 21C12 21 15.3954 18.8605 13.3637 16C12.0647 14.1711 9.51275 11.9823 9 10C8 6.134 10.134 3 12 3C13.866 3 16 6.134 15 10C14.4873 11.9823 11.9353 14.1711 10.6363 16C8.60464 18.8605 12 21 12 21Z", duration: 0.1 },
                        { morphSVG: "M12 21C12 21 14.0216 19.0215 14.3637 16C14.6026 13.8898 13.5128 11.9823 13 10C12 6.134 13.134 3 12 3C10.866 3 12 6.134 11 10C10.4873 11.9823 9.39736 13.8898 9.6363 16C9.97843 19.0215 12 21 12 21Z", duration: 0.05 },
                        {
                            morphSVG: "M20 4H4c-1.1 0-2 .9-2 2v12c0 1.1.9 2 2 2h16c1.1 0 2-.9 2-2V6c0-1.1-.9-2-2-2zm0 4-8 5-8-5V6l8 5 8-5v2z",
                            duration: 0.7, ease: "elastic.out(1, .9)",
                            onStart: () => {
                                gsap.to(button, { "--tab-bar-email-scale": 0.7, duration: 0.7, ease: "elastic.out(1, .9)" });
                                gsap.to(button, { "--tab-bar-email-opacity": 1, duration: 0.2 });
                            }
                        }
                    ]
                });
            }
        });
    };

    const animateLinkedIn = () => {
        if (!buttonRef.current || !pathRef.current) return;
        const button = buttonRef.current;
        const path = pathRef.current;
        gsap.to(button, {
            "--tab-bar-linkedin-scale": 0.25, "--tab-bar-linkedin-opacity": 0, duration: 0.1, onComplete: () => {
                gsap.to(path, {
                    keyframes: [
                        { morphSVG: "M12 21C12 21 15.3954 18.8605 13.3637 16C12.0647 14.1711 9.51275 11.9823 9 10C8 6.134 10.134 3 12 3C13.866 3 16 6.134 15 10C14.4873 11.9823 11.9353 14.1711 10.6363 16C8.60464 18.8605 12 21 12 21Z", duration: 0.125 },
                        { morphSVG: "M13.364 5.63604C14.0062 9.12971 7.68417 13.4401 8.36401 18.3639C8.84929 21.8787 15.1508 21.8787 15.6361 18.3639C16.316 13.4401 9.99389 9.12969 10.6361 5.63604C11.3564 1.71793 12.6438 1.71795 13.364 5.63604Z", duration: 0.05 },
                        {
                            morphSVG: "M19 0h-14c-2.761 0-5 2.239-5 5v14c0 2.761 2.239 5 5 5h14c2.762 0 5-2.239 5-5v-14c0-2.761-2.238-5-5-5zm-11 19h-3v-11h3v11zm-1.5-12.268c-.966 0-1.75-.79-1.75-1.764s.784-1.764 1.75-1.764 1.75.79 1.75 1.764-.783 1.764-1.75 1.764zm13.5 12.268h-3v-5.604c0-3.368-4-3.113-4 0v5.604h-3v-11h3v1.765c1.396-2.586 7-2.777 7 2.476v6.759z",
                            duration: 0.8, ease: "elastic.out(1, .9)",
                            onStart: () => {
                                gsap.to(button, { "--tab-bar-linkedin-scale": 0.7, duration: 0.8, ease: "elastic.out(1, .9)" });
                                gsap.to(button, { "--tab-bar-linkedin-opacity": 1, duration: 0.2 });
                            }
                        }
                    ]
                });
            }
        });
    };

    const animateX = () => {
        if (!buttonRef.current || !pathRef.current) return;
        const button = buttonRef.current;
        const path = pathRef.current;

        // Morphing animation for X
        gsap.to(button, {
            "--tab-bar-x-scale": 0.25,
            "--tab-bar-x-opacity": 0,
            duration: 0.1,
            onComplete: () => {
                gsap.to(path, {
                    keyframes: [
                        {
                            morphSVG: "M12 21C12 21 15.3954 18.8605 13.3637 16C12.0647 14.1711 9.51275 11.9823 9 10C8 6.134 10.134 3 12 3C13.866 3 16 6.134 15 10C14.4873 11.9823 11.9353 14.1711 10.6363 16C8.60464 18.8605 12 21 12 21Z",
                            duration: 0.1
                        },
                        {
                            morphSVG: "M12 21C12 21 14.0216 19.0215 14.3637 16C14.6026 13.8898 13.5128 11.9823 13 10C12 6.134 13.134 3 12 3C10.866 3 12 6.134 11 10C10.4873 11.9823 9.39736 13.8898 9.6363 16C9.97843 19.0215 12 21 12 21Z",
                            duration: 0.05
                        },
                        {
                            // Return to X shape
                            morphSVG: "M18.901 1.153h3.68l-8.04 9.19L24 22.846h-7.406l-5.8-7.584-6.638 7.584H.474l8.6-9.83L0 1.154h7.594l5.243 6.932ZM17.61 20.644h2.039L6.486 3.24H4.298Z",
                            duration: 0.75,
                            ease: "elastic.out(1, .9)",
                            onStart: () => {
                                gsap.to(button, {
                                    "--tab-bar-x-scale": 0.7,
                                    duration: 0.75,
                                    ease: "elastic.out(1, .9)"
                                });
                                gsap.to(button, {
                                    "--tab-bar-x-opacity": 1,
                                    duration: 0.2
                                });
                            }
                        }
                    ]
                });
            }
        });
    };

    const animateGithub = () => {
        if (!buttonRef.current || !pathRef.current) return;
        const button = buttonRef.current;
        const path = pathRef.current;

        // Morphing animation for Github
        gsap.to(button, {
            "--tab-bar-github-scale": 0.25,
            "--tab-bar-github-opacity": 0,
            duration: 0.1,
            onComplete: () => {
                gsap.to(path, {
                    keyframes: [
                        {
                            morphSVG: "M12 21C12 21 15.3954 18.8605 13.3637 16C12.0647 14.1711 9.51275 11.9823 9 10C8 6.134 10.134 3 12 3C13.866 3 16 6.134 15 10C14.4873 11.9823 11.9353 14.1711 10.6363 16C8.60464 18.8605 12 21 12 21Z",
                            duration: 0.1,
                        },
                        {
                            morphSVG: "M12 21C12 21 14.0216 19.0215 14.3637 16C14.6026 13.8898 13.5128 11.9823 13 10C12 6.134 13.134 3 12 3C10.866 3 12 6.134 11 10C10.4873 11.9823 9.39736 13.8898 9.6363 16C9.97843 19.0215 12 21 12 21Z",
                            duration: 0.05,
                        },
                        {
                            // Return to Github shape
                            morphSVG: "M12 0c-6.626 0-12 5.373-12 12 0 5.302 3.438 9.8 8.207 11.387.599.111.793-.261.793-.577v-2.234c-3.338.726-4.033-1.416-4.033-1.416-.546-1.387-1.333-1.756-1.333-1.756-1.089-.745.083-.729.083-.729 1.205.084 1.839 1.237 1.839 1.237 1.07 1.834 2.807 1.304 3.492.997.107-.775.418-1.305.762-1.604-2.665-.305-5.467-1.334-5.467-5.931 0-1.311.469-2.381 1.236-3.221-.124-.303-.535-1.524.117-3.176 0 0 1.008-.322 3.301 1.23.957-.266 1.983-.399 3.003-.404 1.02.005 2.047.138 3.006.404 2.291-1.552 3.297-1.23 3.297-1.23.653 1.653.242 2.874.118 3.176.77.84 1.235 1.911 1.235 3.221 0 4.609-2.807 5.624-5.479 5.921.43.372.823 1.102.823 2.222v3.293c0 .319.192.694.801.576 4.765-1.589 8.199-6.086 8.199-11.386 0-6.627-5.373-12-12-12z",
                            duration: 0.75,
                            ease: "elastic.out(1, .9)",
                            onStart: () => {
                                gsap.to(button, {
                                    "--tab-bar-github-scale": 0.7,
                                    duration: 0.75,
                                    ease: "elastic.out(1, .9)",
                                });
                                gsap.to(button, {
                                    "--tab-bar-github-opacity": 1,
                                    duration: 0.2,
                                });
                            },
                        },
                    ],
                });
            },
        });
    };

    const handleMouseEnter = () => {
        onMouseEnter && onMouseEnter();
        if (type === 'home') animateHome();
        else if (type === 'blog') animateBlog();
        else if (type === 'marker') animateMarker();
        else if (type === 'email') animateEmail();
        else if (type === 'linkedin') animateLinkedIn();
        else if (type === 'x') animateX();
        else if (type === 'github') animateGithub();
    };

    if (type === 'home') {
        return (
            <button ref={buttonRef} onClick={onClick} onMouseEnter={handleMouseEnter} className={cn("home", isActive ? "active" : "")}>
                <svg viewBox="0 0 24 24">
                    <path ref={pathRef} d="M3 18V10.5339C3 9.57062 3.46259 8.66591 4.24353 8.1019L10.2435 3.76856C11.2921 3.01128 12.7079 3.01128 13.7565 3.76856L19.7565 8.1019C20.5374 8.66591 21 9.57062 21 10.5339V18C21 19.6569 19.6569 21 18 21H6C4.34315 21 3 19.6569 3 18Z" />
                </svg>
            </button>
        );
    }
    if (type === 'blog') {
        return (
            <button ref={buttonRef} onClick={onClick} onMouseEnter={handleMouseEnter} className={cn("blog", isActive ? "active" : "")}>
                <svg viewBox="0 0 24 24">
                    <path ref={pathRef} d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zm-5 14H7v-2h7v2zm3-4H7v-2h10v2zm0-4H7V7h10v2z" />
                </svg>
            </button>
        );
    }
    if (type === 'marker') {
        return (
            <button ref={buttonRef} onClick={onClick} onMouseEnter={handleMouseEnter} className={cn("marker", isActive ? "active" : "")}>
                <svg viewBox="0 0 24 24">
                    <path ref={pathRef} d="M12 21C12 21 9.39536 18.8605 7.3637 16C6.06474 14.1711 5 12.0475 5 10C5 6.134 8.134 3 12 3C15.866 3 19 6.134 19 10C19 12.0475 17.9353 14.1711 16.6363 16C14.6046 18.8605 12 21 12 21Z" />
                </svg>
            </button>
        );
    }
    if (type === 'email') {
        return (
            <button ref={buttonRef} onClick={onClick} onMouseEnter={handleMouseEnter} className={cn("email", isActive ? "active" : "")}>
                <svg viewBox="0 0 24 24">
                    <path ref={pathRef} d="M20 4H4c-1.1 0-2 .9-2 2v12c0 1.1.9 2 2 2h16c1.1 0 2-.9 2-2V6c0-1.1-.9-2-2-2zm0 4-8 5-8-5V6l8 5 8-5v2z" />
                </svg>
            </button>
        );
    }
    if (type === 'linkedin') {
        return (
            <button ref={buttonRef} onClick={onClick} onMouseEnter={handleMouseEnter} className={cn("linkedin", isActive ? "active" : "")}>
                <svg viewBox="0 0 24 24">
                    <path ref={pathRef} d="M19 0h-14c-2.761 0-5 2.239-5 5v14c0 2.761 2.239 5 5 5h14c2.762 0 5-2.239 5-5v-14c0-2.761-2.238-5-5-5zm-11 19h-3v-11h3v11zm-1.5-12.268c-.966 0-1.75-.79-1.75-1.764s.784-1.764 1.75-1.764 1.75.79 1.75 1.764-.783 1.764-1.75 1.764zm13.5 12.268h-3v-5.604c0-3.368-4-3.113-4 0v5.604h-3v-11h3v1.765c1.396-2.586 7-2.777 7 2.476v6.759z" />
                </svg>
            </button>
        );
    }
    if (type === 'x') {
        return (
            <button ref={buttonRef} onClick={onClick} onMouseEnter={handleMouseEnter} className={cn("x", isActive ? "active" : "")}>
                <svg viewBox="0 0 24 24">
                    <path ref={pathRef} d="M18.901 1.153h3.68l-8.04 9.19L24 22.846h-7.406l-5.8-7.584-6.638 7.584H.474l8.6-9.83L0 1.154h7.594l5.243 6.932ZM17.61 20.644h2.039L6.486 3.24H4.298Z" />
                </svg>
            </button>
        );
    }
    if (type === 'github') {
        return (
            <button ref={buttonRef} onClick={onClick} onMouseEnter={handleMouseEnter} className={cn("github", isActive ? "active" : "")}>
                <svg viewBox="0 0 24 24">
                    <path ref={pathRef} d="M12 0c-6.626 0-12 5.373-12 12 0 5.302 3.438 9.8 8.207 11.387.599.111.793-.261.793-.577v-2.234c-3.338.726-4.033-1.416-4.033-1.416-.546-1.387-1.333-1.756-1.333-1.756-1.089-.745.083-.729.083-.729 1.205.084 1.839 1.237 1.839 1.237 1.07 1.834 2.807 1.304 3.492.997.107-.775.418-1.305.762-1.604-2.665-.305-5.467-1.334-5.467-5.931 0-1.311.469-2.381 1.236-3.221-.124-.303-.535-1.524.117-3.176 0 0 1.008-.322 3.301 1.23.957-.266 1.983-.399 3.003-.404 1.02.005 2.047.138 3.006.404 2.291-1.552 3.297-1.23 3.297-1.23.653 1.653.242 2.874.118 3.176.77.84 1.235 1.911 1.235 3.221 0 4.609-2.807 5.624-5.479 5.921.43.372.823 1.102.823 2.222v3.293c0 .319.192.694.801.576 4.765-1.589 8.199-6.086 8.199-11.386 0-6.627-5.373-12-12-12z" fill="currentColor" stroke="none" fillRule="evenodd" clipRule="evenodd" />
                </svg>
            </button>
        );
    }
    return null;
};

export const GlassDock = React.forwardRef<HTMLDivElement, GlassDockProps>(
    (
        {
            items,
            className,
            dockClassName,
            ...props
        },
        ref
    ) => {
        const [hoveredIndex, setHoveredIndex] = useState<number | null>(null);
        const [direction, setDirection] = useState(0);
        // Default to not darker for dock itself, but buttons handle colors
        // We removed isDark check as styles in globals.css handle it via .dark selector

        const handleMouseEnter = (index: number) => {
            if (hoveredIndex !== null && index !== hoveredIndex) {
                setDirection(index > hoveredIndex ? 1 : -1);
            }
            setHoveredIndex(index);
        };

        const getTooltipPosition = (index: number) => index * 52 + 12;

        return (
            <div
                ref={ref}
                className={cn('w-max', className)}
                {...props}
            >
                <div
                    className={cn(
                        "glass-dock relative flex gap-4 items-center px-6 py-4 rounded-2xl",
                        "glass-border bg-white/80 dark:bg-black/80",
                        "backdrop-blur-xl shadow-2xl justify-center",
                        dockClassName
                    )}
                    onMouseLeave={() => {
                        setHoveredIndex(null);
                        setDirection(0);
                    }}
                >
                    <AnimatePresence>
                        {hoveredIndex !== null && (
                            <motion.div
                                layout
                                initial={{ opacity: 0, scale: 0.92, y: 12 }}
                                animate={{
                                    opacity: 1,
                                    scale: 1,
                                    y: -60,
                                    x: getTooltipPosition(hoveredIndex),
                                }}
                                exit={{ opacity: 0, scale: 0.92, y: 12 }}
                                transition={{ type: 'spring', stiffness: 120, damping: 18 }}
                                className="absolute top-0 left-0 pointer-events-none z-30"
                            >
                                <div
                                    className={cn(
                                        'px-5 py-2 rounded-lg',
                                        'bg-black text-white dark:bg-white dark:text-black',
                                        'shadow-md flex items-center justify-center',
                                        'border border-neutral-700 dark:border-neutral-300',
                                        'min-w-[100px] '
                                    )}
                                >
                                    <div className="relative h-4 flex items-center justify-center overflow-hidden w-full">
                                        <AnimatePresence mode="popLayout" custom={direction}>
                                            <motion.span
                                                key={items[hoveredIndex].title}
                                                custom={direction}
                                                initial={{
                                                    x: direction > 0 ? 35 : -35,
                                                    opacity: 0,
                                                    filter: 'blur(6px)',
                                                }}
                                                animate={{
                                                    x: 0,
                                                    opacity: 1,
                                                    filter: 'blur(0px)',
                                                }}
                                                exit={{
                                                    x: direction > 0 ? -35 : 35,
                                                    opacity: 0,
                                                    filter: 'blur(6px)',
                                                }}
                                                transition={{
                                                    duration: 0.3,
                                                    ease: 'easeOut',
                                                }}
                                                className="text-[13px] font-medium tracking-wide whitespace-nowrap"
                                            >
                                                {items[hoveredIndex].title}
                                            </motion.span>
                                        </AnimatePresence>
                                    </div>
                                </div>
                            </motion.div>
                        )}
                    </AnimatePresence>

                    {items.map((el, index) => {
                        const Icon = el.icon;
                        const isHovered = hoveredIndex === index;
                        const isActive = isHovered; // Simplified for this demo

                        const handleClick = () => {
                            if (el.onClick) {
                                el.onClick();
                            } else if (el.href) {
                                window.location.href = el.href;
                            }
                        };

                        // Check if title matches one of our animated icons
                        const type = el.title.toLowerCase();
                        const isAnimated = ['home', 'blog', 'marker', 'email', 'linkedin', 'x', 'github'].includes(type);

                        return (
                            <div
                                key={el.title}
                                onMouseEnter={() => handleMouseEnter(index)}
                                onClick={handleClick}
                                className="relative w-10 h-10 flex items-center justify-center cursor-pointer"
                                role="button"
                                tabIndex={0}
                                onKeyDown={(e) => {
                                    if (e.key === 'Enter' || e.key === ' ') {
                                        handleClick();
                                    }
                                }}
                            >
                                <motion.div
                                    whileTap={{ scale: 0.95 }}
                                    animate={{
                                        scale: isHovered ? 1.1 : 1,
                                        y: isHovered ? -3 : 0,
                                    }}
                                    transition={{ type: 'spring', stiffness: 300, damping: 24 }}
                                >
                                    {isAnimated ? (
                                        <MorphingIcon
                                            type={type}
                                            isActive={isActive}
                                            onClick={handleClick}
                                            // Passing handleMouseEnter down if we want internal hover trigger as well
                                            // But standard tooltip enter is handled by parent div
                                            // We trigger internal GSAP here
                                            onMouseEnter={() => { }}
                                        />
                                    ) : (
                                        <Icon
                                            className={cn(
                                                'h-[22px] w-[22px] transition-colors duration-200',
                                                isHovered
                                                    ? 'text-neutral-900 dark:text-white'
                                                    : 'text-neutral-500 dark:text-neutral-400'
                                            )}
                                        />
                                    )}
                                </motion.div>
                            </div>
                        );
                    })}
                </div>
            </div>
        );
    }
);

GlassDock.displayName = 'GlassDock';
export default GlassDock;
```

---

## `src\components\ui\glow-border-card.tsx`

```tsx
'use client';

import React from 'react';
import { cn } from '@/lib/utils';

/**
 * Props for the GlowBorderCard component
 */
export interface GlowBorderCardProps extends React.HTMLAttributes<HTMLDivElement> {
    /**
     * Content to display inside the card
     */
    children?: React.ReactNode;

    /**
     * Width of the card (CSS value)
     * @default "320px"
     */
    width?: string;

    /**
     * Height of the card (CSS value). If not provided, uses aspect-ratio.
     */
    height?: string;

    /**
     * Aspect ratio of the card (e.g., "1", "16/9", "4/3")
     * @default "1"
     */
    aspectRatio?: string;

    /**
     * Corner radius of the card
     * @default "0.75rem"
     */
    borderRadius?: string;

    /**
     * Animation duration in seconds
     * @default 4
     */
    animationDuration?: number;

    /**
     * Gradient colors array (up to 10 colors)
     */
    gradientColors?: string[];

    /**
     * Border width for the glow effect
     * @default "1.25em"
     */
    borderWidth?: string;

    /**
     * Blur amount for the glow effect
     * @default "0.75em"
     */
    blurAmount?: string;

    /**
     * Inset distance (negative values push the border outside)
     * @default "-1em"
     */
    inset?: string;

    /**
     * Preset color themes
     */
    colorPreset?: 'nature' | 'ocean' | 'sunset' | 'aurora' | 'custom';

    /**
     * Whether animation is paused
     * @default false
     */
    paused?: boolean;
}

// Preset gradient colors (10 colors each for smooth transitions)
const colorPresets: Record<string, string[]> = {
    nature: ['#669900', '#88bb22', '#99cc33', '#aaddaa', '#ccee66', '#006699', '#228888', '#3399cc', '#55aacc', '#669900'],
    ocean: ['#006699', '#1177aa', '#2288bb', '#3399cc', '#44aadd', '#55bbee', '#66ccff', '#44bbee', '#2299cc', '#006699'],
    sunset: ['#ff6600', '#ff7711', '#ff8822', '#ff9900', '#ffaa22', '#ffbb44', '#ffcc00', '#ff9933', '#ff7722', '#ff6600'],
    aurora: ['#00ff87', '#22ffaa', '#44ffcc', '#60efff', '#88ddff', '#bb99ff', '#dd77ee', '#ff68f0', '#ff55cc', '#00ff87'],
    custom: ['#669900', '#99cc33', '#ccee66', '#006699', '#3399cc', '#990066', '#cc3399', '#ff6600', '#ff9900', '#ffcc00'],
};

/**
 * GlowBorderCard - A CSS-only animated glowing border card component
 * 
 * Features a rotating conic gradient that creates a beautiful
 * aurora-like glow effect around the card edges.
 * Uses @property for smooth angle animation.
 */
export const GlowBorderCard = React.forwardRef<HTMLDivElement, GlowBorderCardProps>(
    (
        {
            children,
            className,
            width = '320px',
            height,
            aspectRatio = '1',
            borderRadius = '0.75rem',
            animationDuration = 4,
            gradientColors,
            borderWidth = '1.25em',
            blurAmount = '0.75em',
            inset = '-1em',
            colorPreset = 'custom',
            paused = false,
            style,
            ...props
        },
        ref
    ) => {
        // Determine the gradient colors to use (up to 10)
        const colors = gradientColors || colorPresets[colorPreset] || colorPresets.custom;

        // Build color CSS variables (--glow-color-1 through --glow-color-10)
        const colorVars: Record<string, string> = {};
        for (let i = 0; i < 10; i++) {
            colorVars[`--glow-color-${i + 1}`] = colors[i % colors.length];
        }

        return (
            <div
                ref={ref}
                className={cn(
                    // Base Container Layout
                    "relative overflow-hidden grid place-content-center isolate",
                    // Glass Effect Background
                    "bg-zinc-50/50 dark:bg-neutral-900/60 backdrop-blur-md",
                    // Custom className support
                    className
                )}
                style={{
                    width: width,
                    height: height || 'auto',
                    aspectRatio: height ? 'unset' : aspectRatio,
                    borderRadius: borderRadius,
                    '--glow-animation-duration': `${animationDuration}s`,
                    ...colorVars,
                    ...style,
                } as React.CSSProperties}
                {...props}
            >
                {/* 
                  The Glow Pseudo-Element Replacer 
                  Using a real div instead of ::before allows better Tailwind control 
                */}
                <div
                    className={cn(
                        "absolute -z-10",
                        // Inset logic handled by style or arbitrary values if fixed
                        "border-solid rounded-[inherit]",
                        // The Gradient Animation Class
                        "glow-conic",
                        // Pause State
                        paused && "[animation-play-state:paused]"
                    )}
                    style={{
                        inset: inset,
                        borderWidth: borderWidth,
                        filter: `blur(${blurAmount})`
                    }}
                />

                {/* Content Container */}
                <div
                    className="relative z-10 w-full h-full bg-transparent flex items-center justify-center p-4"
                >
                    {children}
                </div>
            </div>
        );
    }
);

GlowBorderCard.displayName = 'GlowBorderCard';

export default GlowBorderCard;

```

---

## `src\components\ui\gooey-search.tsx`

```tsx
"use client";

import { useState, useRef, useEffect, useMemo, useId } from "react";
import { AnimatePresence, motion } from "framer-motion";
import { cn } from "@/lib/utils";

// ── Utilities ────────────────────────────────────────────────────────────────

function detectUnsupportedBrowser(): boolean {
  if (typeof navigator === "undefined") return false;
  const ua = navigator.userAgent.toLowerCase();
  const isSafari =
    ua.includes("safari") &&
    !ua.includes("chrome") &&
    !ua.includes("chromium") &&
    !ua.includes("android") &&
    !ua.includes("firefox");
  return isSafari || ua.includes("crios");
}

function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState<T>(value);
  useEffect(() => {
    const handler = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(handler);
  }, [value, delay]);
  return debouncedValue;
}

// ── Animation variants ───────────────────────────────────────────────────────

const buttonMotionVariants = {
  step1: { x: 0, width: 100 },
  step2: { x: -30, width: 180 },
};

const iconMotionVariants = {
  hidden: { x: -50, opacity: 0 },
  visible: { x: 16, opacity: 1 },
};

const getResultVariants = (index: number, unsupported: boolean) => ({
  initial: { y: 0, scale: 0.3, filter: unsupported ? "none" : "blur(10px)" },
  animate: { y: (index + 1) * 50, scale: 1, filter: "blur(0px)" },
  exit: { y: unsupported ? 0 : -4, scale: 0.8 },
});

const getResultTransition = (index: number) => ({
  duration: 0.75,
  delay: index * 0.12,
  type: "spring" as const,
  bounce: 0.35,
  filter: { ease: "easeInOut" },
});

// ── Private sub-components ───────────────────────────────────────────────────

function SearchSvgIcon({ isUnsupported }: { isUnsupported: boolean }) {
  return (
    <motion.svg
      initial={{ opacity: 0, scale: 0.8, x: -4, filter: isUnsupported ? "none" : "blur(5px)" }}
      animate={{ opacity: 1, scale: 1, x: 0, filter: "blur(0px)" }}
      exit={{ opacity: 0, scale: 0.8, x: -4, filter: isUnsupported ? "none" : "blur(5px)" }}
      transition={{ delay: 0.1, duration: 1, type: "spring", bounce: 0.15 }}
      width="15"
      height="15"
      viewBox="0 0 15 15"
      fill="none"
      xmlns="http://www.w3.org/2000/svg"
    >
      <path
        d="M10 6.5C10 8.433 8.433 10 6.5 10C4.567 10 3 8.433 3 6.5C3 4.567 4.567 3 6.5 3C8.433 3 10 4.567 10 6.5ZM9.30884 10.0159C8.53901 10.6318 7.56251 11 6.5 11C4.01472 11 2 8.98528 2 6.5C2 4.01472 4.01472 2 6.5 2C8.98528 2 11 4.01472 11 6.5C11 7.56251 10.6318 8.53901 10.0159 9.30884L12.8536 12.1464C13.0488 12.3417 13.0488 12.6583 12.8536 12.8536C12.6583 13.0488 12.3417 13.0488 12.1464 12.8536L9.30884 10.0159Z"
        fillRule="evenodd"
        clipRule="evenodd"
        fill="currentColor"
      />
    </motion.svg>
  );
}

function LoadingSvgIcon() {
  const lines: [number, number, number, number][] = [
    [128, 32, 128, 64],
    [195.88, 60.12, 173.25, 82.75],
    [224, 128, 192, 128],
    [195.88, 195.88, 173.25, 173.25],
    [128, 224, 128, 192],
    [60.12, 195.88, 82.75, 173.25],
    [32, 128, 64, 128],
    [60.12, 60.12, 82.75, 82.75],
  ];
  return (
    <svg
      className="gooey-search-loading"
      xmlns="http://www.w3.org/2000/svg"
      viewBox="0 0 256 256"
      aria-label="Loading"
      role="status"
      style={{ width: 20, height: 20 }}
    >
      <rect width="256" height="256" fill="none" />
      {lines.map(([x1, y1, x2, y2], i) => (
        <line
          key={i}
          x1={x1} y1={y1} x2={x2} y2={y2}
          stroke="currentColor"
          strokeLinecap="round"
          strokeLinejoin="round"
          strokeWidth={16}
        />
      ))}
    </svg>
  );
}

function InfoSvgIcon({ index }: { index: number }) {
  return (
    <motion.svg
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      exit={{ opacity: 0 }}
      transition={{ delay: index * 0.12 + 0.3 }}
      xmlns="http://www.w3.org/2000/svg"
      viewBox="0 0 15 15"
      fill="none"
      aria-hidden="true"
      style={{ width: 18, height: 18, position: "relative", top: 2, flexShrink: 0 }}
    >
      <path
        d="M7.49991 0.876892C3.84222 0.876892 0.877075 3.84204 0.877075 7.49972C0.877075 11.1574 3.84222 14.1226 7.49991 14.1226C11.1576 14.1226 14.1227 11.1574 14.1227 7.49972C14.1227 3.84204 11.1576 0.876892 7.49991 0.876892ZM1.82707 7.49972C1.82707 4.36671 4.36689 1.82689 7.49991 1.82689C10.6329 1.82689 13.1727 4.36671 13.1727 7.49972C13.1727 10.6327 10.6329 13.1726 7.49991 13.1726C4.36689 13.1726 1.82707 10.6327 1.82707 7.49972ZM8.24992 4.49999C8.24992 4.9142 7.91413 5.24999 7.49992 5.24999C7.08571 5.24999 6.74992 4.9142 6.74992 4.49999C6.74992 4.08577 7.08571 3.74999 7.49992 3.74999C7.91413 3.74999 8.24992 4.08577 8.24992 4.49999ZM6.00003 5.99999H6.50003H7.50003C7.77618 5.99999 8.00003 6.22384 8.00003 6.49999V9.99999H8.50003H9.00003V11H8.50003H7.50003H6.50003H6.00003V9.99999H6.50003H7.00003V6.99999H6.50003H6.00003V5.99999Z"
        fill="currentColor"
        fillRule="evenodd"
        clipRule="evenodd"
      />
    </motion.svg>
  );
}

// ── Public types ─────────────────────────────────────────────────────────────

export interface GooeySearchProps {
  /** Strings to search locally. Ignored when `onSearch` is provided. */
  items?: string[];
  /** Async/sync custom search function for external data sources. */
  onSearch?: (query: string) => Promise<string[]> | string[];
  /** Input placeholder text. */
  placeholder?: string;
  /** Label shown on the collapsed button. */
  buttonLabel?: string;
  /** Called when the user clicks a result item. */
  onSelect?: (item: string) => void;
  /** Extra class names for the outermost wrapper. */
  className?: string;
  /** Input debounce delay in ms. Defaults to 500. */
  debounceMs?: number;
  /** Maximum number of results to render. Defaults to 5. */
  maxResults?: number;
}

// ── Component ────────────────────────────────────────────────────────────────

export function GooeySearch({
  items = [],
  onSearch,
  placeholder = "Type to search...",
  buttonLabel = "Search",
  onSelect,
  className,
  debounceMs = 500,
  maxResults = 5,
}: GooeySearchProps) {
  const uid = useId().replace(/:/g, "_");
  const filterId = `gooey-search-${uid}`;

  const inputRef = useRef<HTMLInputElement>(null);
  const [step, setStep] = useState<1 | 2>(1);
  const [searchText, setSearchText] = useState("");
  const [results, setResults] = useState<string[]>([]);
  const [isLoading, setIsLoading] = useState(false);

  const isUnsupported = useMemo(() => detectUnsupportedBrowser(), []);
  const debouncedQuery = useDebounce(searchText, debounceMs);

  // Step only ever advances 1 -> 2, so the effect's sole job is to focus the
  // freshly-mounted input. Nothing to reset here.
  useEffect(() => {
    if (step === 2) inputRef.current?.focus();
  }, [step]);

  useEffect(() => {
    let cancelled = false;

    // All state writes live inside the async closure so none run synchronously
    // in the effect body (keeps React from cascading renders).
    const run = async () => {
      if (!debouncedQuery) {
        setResults([]);
        setIsLoading(false);
        return;
      }

      setIsLoading(true);
      try {
        let data: string[];
        if (onSearch) {
          data = await onSearch(debouncedQuery);
        } else {
          await new Promise<void>((r) => setTimeout(r, 300));
          data = items.filter((item) =>
            item.toLowerCase().includes(debouncedQuery.trim().toLowerCase())
          );
        }
        if (!cancelled) setResults(data.slice(0, maxResults));
      } finally {
        if (!cancelled) setIsLoading(false);
      }
    };

    run();
    return () => { cancelled = true; };
  }, [debouncedQuery, items, onSearch, maxResults]);

  const btnPadding = isUnsupported ? "5px 10px" : "10px 20px";
  const resultPadding = isUnsupported ? "7.5px 10px" : "12.5px 20px";

  return (
    <div className={cn("relative inline-flex items-center justify-center", className)}>
      {/* Keyframe injection for loading spinner */}
      <style>{`
        .gooey-search-loading {
          animation: gooeySearchSpin 0.5s linear infinite;
          transform-origin: center center;
        }
        @keyframes gooeySearchSpin { to { transform: rotate(180deg); } }
        .gooey-search-input::placeholder { color: var(--background); opacity: 0.55; }
      `}</style>

      {/* SVG gooey filter — zero size, no layout impact */}
      <svg aria-hidden="true" style={{ position: "absolute", width: 0, height: 0, overflow: "hidden" }}>
        <defs>
          <filter id={filterId}>
            <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur" />
            <feColorMatrix
              in="blur"
              type="matrix"
              values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 18 -15"
              result="goo"
            />
            <feComposite in="SourceGraphic" in2="goo" operator="atop" />
          </filter>
        </defs>
      </svg>

      {/* Gooey container — this is where the morphing magic happens */}
      <div
        style={{
          filter: isUnsupported ? "none" : `url(#${filterId})`,
          cursor: "pointer",
          maxWidth: "max-content",
          position: "relative",
        }}
      >
        {/* Results — z-index -1 so they live "behind" the button until animated out */}
        <AnimatePresence mode="popLayout">
          <motion.div
            key="results-wrapper"
            role="listbox"
            aria-label="Search results"
            style={{ position: "relative", zIndex: -1 }}
            exit={{ scale: 0, opacity: 0 }}
            transition={{ delay: isUnsupported ? 0.5 : 1.25, duration: 0.5 }}
          >
            <AnimatePresence mode="popLayout">
              {results.map((item, index) => (
                <motion.div
                  key={item}
                  role="option"
                  tabIndex={0}
                  onClick={() => onSelect?.(item)}
                  onKeyDown={(e) => e.key === "Enter" && onSelect?.(item)}
                  whileHover={{ scale: 1.02, transition: { duration: 0.2 } }}
                  variants={getResultVariants(index, isUnsupported)}
                  initial="initial"
                  animate="animate"
                  exit="exit"
                  transition={getResultTransition(index)}
                  style={{
                    backgroundColor: "var(--foreground)",
                    borderRadius: 40,
                    padding: resultPadding,
                    width: "100%",
                    color: "var(--background)",
                    position: "absolute",
                    left: isUnsupported ? 0 : -30,
                    fontSize: 14,
                    cursor: "pointer",
                  }}
                >
                  <div style={{ display: "flex", alignItems: "center", gap: 4 }}>
                    <InfoSvgIcon index={index} />
                    <motion.span
                      initial={{ opacity: 0 }}
                      animate={{ opacity: 1 }}
                      transition={{ delay: index * 0.12 + 0.3 }}
                      style={{ position: "relative", top: -0.35 }}
                    >
                      {item}
                    </motion.span>
                  </div>
                </motion.div>
              ))}
            </AnimatePresence>
          </motion.div>
        </AnimatePresence>

        {/* Morphing search button */}
        <motion.div
          variants={buttonMotionVariants}
          initial="step1"
          animate={step === 1 ? "step1" : "step2"}
          transition={{ duration: 0.75, type: "spring", bounce: 0.15 }}
          onClick={() => step === 1 && setStep(2)}
          whileHover={{ scale: step === 2 ? 1 : 1.05 }}
          whileTap={{ scale: 0.95 }}
          role={step === 1 ? "button" : undefined}
          aria-label={step === 1 ? "Open search" : undefined}
          style={{
            backgroundColor: "var(--foreground)",
            color: "var(--background)",
            cursor: "pointer",
            letterSpacing: -0.5,
            outline: "none",
            border: "none",
            borderRadius: 9999,
            padding: btnPadding,
          }}
        >
          {step === 1 ? (
            <span
              style={{
                pointerEvents: "none",
                textAlign: "center",
                position: "relative",
                left: 4,
                color: "var(--background)",
                opacity: 0.72,
                fontSize: 14,
                display: "block",
              }}
            >
              {buttonLabel}
            </span>
          ) : (
            <input
              ref={inputRef}
              type="text"
              className="gooey-search-input"
              placeholder={placeholder}
              aria-label="Search input"
              onChange={(e) => setSearchText(e.target.value)}
              style={{
                width: "100%",
                backgroundColor: "transparent",
                outline: "none",
                border: "none",
                color: "var(--background)",
                fontSize: 14,
              }}
            />
          )}
        </motion.div>

        {/* Floating icon bubble */}
        <AnimatePresence mode="wait">
          {step === 2 && (
            <motion.div
              key="icon-bubble"
              initial="hidden"
              animate="visible"
              exit="hidden"
              variants={iconMotionVariants}
              transition={{ delay: 0.1, duration: 0.85, type: "spring", bounce: 0.15 }}
              style={{
                position: "absolute",
                backgroundColor: "var(--foreground)",
                width: isUnsupported ? 36 : 46,
                height: isUnsupported ? 36 : 46,
                right: -5,
                top: -1,
                display: "flex",
                justifyContent: "center",
                alignItems: "center",
                borderRadius: 9999,
                color: "var(--background)",
              }}
            >
              {isLoading ? <LoadingSvgIcon /> : <SearchSvgIcon isUnsupported={isUnsupported} />}
            </motion.div>
          )}
        </AnimatePresence>
      </div>
    </div>
  );
}

export default GooeySearch;

```

---

## `src\components\ui\highlight-grid.tsx`

```tsx
"use client";

import * as React from "react";
import { useCallback, useEffect, useMemo, useRef, useState } from "react";
import { cn } from "@/lib/utils";

/**
 * Highlight Grid
 *
 * A grid of labelled cells with a single coloured highlight that glides to sit
 * behind whichever cell the cursor is over — morphing its position, size and
 * colour with a smooth transition. Each cell carries its own accent colour, and
 * rows can hold any number of cells.
 *
 * Ported 1:1 from the vanilla "CodeGrid Direction-Aware Hover" experiment into
 * a single, self-contained, prop-driven React component. No animation library —
 * the highlight is a CSS transition driven by pointer events.
 */

export interface HighlightItem {
  label: string;
  /** Accent colour for this cell. Falls back to the cycled `colors` palette. */
  color?: string;
}

export interface HighlightGridProps {
  /** Rows of cells. Each row can hold a different number of cells. */
  rows?: HighlightItem[][];
  /** Palette cycled for cells without an explicit `color`. */
  colors?: string[];
  /** Highlight transition duration in ms. Defaults to 250. */
  transitionDuration?: number;
  /** Park the highlight on the first cell on mount. Defaults to true. */
  highlightFirst?: boolean;
  /** Extra classes for the root element. */
  className?: string;
}

const DEFAULT_COLORS = [
  "#E24E1B",
  "#4381C1",
  "#F79824",
  "#04A777",
  "#5B8C5A",
  "#2176FF",
  "#818D92",
  "#22AAA1",
];

const DEFAULT_ROWS: HighlightItem[][] = [
  [{ label: "html" }, { label: "css" }, { label: "javascript" }],
  [{ label: "gsap" }, { label: "scrolltrigger" }, { label: "react" }, { label: "next.js" }, { label: "three.js" }],
];

export function HighlightGrid({
  rows = DEFAULT_ROWS,
  colors = DEFAULT_COLORS,
  transitionDuration = 250,
  highlightFirst = true,
  className,
}: HighlightGridProps) {
  const gridRef = useRef<HTMLDivElement>(null);
  const highlightRef = useRef<HTMLDivElement>(null);
  const cellRefs = useRef<Map<number, HTMLElement>>(new Map());
  const activeRef = useRef<{ gi: number; color: string } | null>(null);
  const [active, setActive] = useState<number | null>(highlightFirst ? 0 : null);

  // Flatten rows into cells with a running global index + resolved colour.
  const gridRows = useMemo(() => {
    let gi = 0;
    return rows.map((row) =>
      row.map((item) => {
        const idx = gi++;
        return { label: item.label, color: item.color ?? colors[idx % colors.length], gi: idx };
      }),
    );
  }, [rows, colors]);

  const moveTo = useCallback((gi: number, color: string) => {
    // The highlight lives inside the grid, so offsets are relative to the grid.
    const grid = gridRef.current;
    const highlight = highlightRef.current;
    const el = cellRefs.current.get(gi);
    if (!grid || !highlight || !el) return;

    const rect = el.getBoundingClientRect();
    const crect = grid.getBoundingClientRect();
    highlight.style.transform = `translate(${rect.left - crect.left}px, ${rect.top - crect.top}px)`;
    highlight.style.width = `${rect.width}px`;
    highlight.style.height = `${rect.height}px`;
    highlight.style.backgroundColor = color;
    activeRef.current = { gi, color };
  }, []);

  // Park on the first cell initially, and keep the highlight aligned on resize.
  useEffect(() => {
    if (highlightFirst && gridRows[0]?.[0]) {
      const first = gridRows[0][0];
      const h = highlightRef.current;
      if (h) {
        // Skip the entry slide on the very first placement by momentarily
        // zeroing the duration — without touching the other transition
        // longhands, so every later move still animates.
        h.style.transitionDuration = "0s";
        moveTo(first.gi, first.color);
        requestAnimationFrame(() => {
          if (h) h.style.transitionDuration = `${transitionDuration}ms`;
        });
      }
    }

    const onResize = () => {
      if (activeRef.current) moveTo(activeRef.current.gi, activeRef.current.color);
    };
    const grid = gridRef.current;
    const ro = grid ? new ResizeObserver(onResize) : null;
    if (grid && ro) ro.observe(grid);
    window.addEventListener("resize", onResize);
    return () => {
      ro?.disconnect();
      window.removeEventListener("resize", onResize);
    };
  }, [gridRows, highlightFirst, moveTo, transitionDuration]);

  return (
    <div
      className={cn(
        "relative flex h-full w-full items-center justify-center overflow-hidden",
        className,
      )}
    >
      <div
        ref={gridRef}
        className="relative mx-auto flex h-[60%] w-[90%] flex-col border border-black/15 dark:border-white/20"
      >
        {/* Sliding highlight — solid accent (which fades between cells) with a
            fixed radiant gradient sheen layered over it. */}
        <div
          ref={highlightRef}
          aria-hidden
          className="pointer-events-none absolute left-0 top-0 z-0"
          style={{
            backgroundImage:
              "radial-gradient(120% 120% at 50% 0%, rgba(255,255,255,0.28), rgba(255,255,255,0) 52%), linear-gradient(180deg, rgba(255,255,255,0) 55%, rgba(0,0,0,0.22))",
            transitionProperty: "transform, width, height, background-color",
            transitionDuration: `${transitionDuration}ms`,
            transitionTimingFunction: "ease",
          }}
        />

        {gridRows.map((row, r) => (
          <div
            key={r}
            className={cn(
              "flex flex-1",
              r < gridRows.length - 1 && "border-b border-black/15 dark:border-white/20",
            )}
          >
            {row.map((cell, c) => {
              const isActive = active === cell.gi;
              return (
                <div
                  key={cell.gi}
                  ref={(el) => {
                    if (el) cellRefs.current.set(cell.gi, el);
                    else cellRefs.current.delete(cell.gi);
                  }}
                  onMouseEnter={() => {
                    setActive(cell.gi);
                    moveTo(cell.gi, cell.color);
                  }}
                  className={cn(
                    "flex h-full flex-1 items-center justify-center",
                    c < row.length - 1 && "border-r border-black/15 dark:border-white/20",
                  )}
                >
                  <p
                    className={cn(
                      "relative z-[2] font-mono text-[13px] font-medium uppercase transition-colors duration-200",
                      isActive ? "text-white" : "text-neutral-600 dark:text-white/70",
                    )}
                  >
                    ( {cell.label} )
                  </p>
                </div>
              );
            })}
          </div>
        ))}
      </div>
    </div>
  );
}

export default HighlightGrid;

```

---

## `src\components\ui\hover-card.tsx`

```tsx
"use client"

import * as React from "react"
import * as HoverCardPrimitive from "@radix-ui/react-hover-card"

import { cn } from "@/lib/utils"

function HoverCard({
  ...props
}: React.ComponentProps<typeof HoverCardPrimitive.Root>) {
  return <HoverCardPrimitive.Root data-slot="hover-card" {...props} />
}

function HoverCardTrigger({
  ...props
}: React.ComponentProps<typeof HoverCardPrimitive.Trigger>) {
  return (
    <HoverCardPrimitive.Trigger data-slot="hover-card-trigger" {...props} />
  )
}

function HoverCardContent({
  className,
  align = "center",
  sideOffset = 4,
  ...props
}: React.ComponentProps<typeof HoverCardPrimitive.Content>) {
  return (
    <HoverCardPrimitive.Portal data-slot="hover-card-portal">
      <HoverCardPrimitive.Content
        data-slot="hover-card-content"
        align={align}
        sideOffset={sideOffset}
        className={cn(
          "bg-popover text-popover-foreground data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0 data-[state=closed]:zoom-out-95 data-[state=open]:zoom-in-95 data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-from-right-2 data-[side=right]:slide-in-from-left-2 data-[side=top]:slide-in-from-bottom-2 z-50 w-64 origin-(--radix-hover-card-content-transform-origin) rounded-md border p-4 shadow-md outline-hidden",
          className
        )}
        {...props}
      />
    </HoverCardPrimitive.Portal>
  )
}

export { HoverCard, HoverCardTrigger, HoverCardContent }

```

---

## `src\components\ui\image-collage.tsx`

```tsx
"use client";

import React, { useState } from "react";
import { motion } from "framer-motion";
import { cn } from "@/lib/utils";

export interface CollageImage {
  src: string;
  x: number;
  y: number;
  rotate: number;
  alt?: string;
}

export interface ImageCollageProps extends React.HTMLAttributes<HTMLDivElement> {
  images: CollageImage[];
  containerClassName?: string;
  imageClassName?: string;
}

export const ImageCollage = React.forwardRef<HTMLDivElement, ImageCollageProps>(
  (
    { images, className, containerClassName, imageClassName, ...props },
    ref
  ) => {
    const [isOrganized, setIsOrganized] = useState(false);

    const toggleLayout = () => {
      setIsOrganized((prev) => !prev);
    };

    return (
      <div
        ref={ref}
        className={cn(
          "flex flex-col items-center justify-center gap-12 select-none w-full min-h-[400px] cursor-pointer",
          className
        )}
        onClick={toggleLayout}
        {...props}
      >
        <div className="text-zinc-800 dark:text-zinc-200 text-xl font-medium tracking-tight">
          Click anywhere to toggle the layout
        </div>
        
        <motion.div className={cn("h-40 flex items-center justify-center", containerClassName)}>
          {images.map((img, i) => (
            <motion.div
              key={i}
              className={cn(
                "w-24 sm:w-32 shrink-0 aspect-[4/5]",
                !isOrganized && "shadow-lg",
                imageClassName
              )}
              initial={{ opacity: 0, scale: 0.7 }}
              transition={{ type: "spring", bounce: 0.6 }}
              animate={{
                opacity: 1,
                scale: 1,
                x: isOrganized ? 0 : img.x,
                y: isOrganized ? 0 : img.y,
                rotate: isOrganized ? 0 : img.rotate,
                zIndex: isOrganized ? 1 : i,
              }}
            >
              <img
                src={img.src}
                alt={img.alt || `Collage image ${i}`}
                className="w-full h-full object-cover"
              />
            </motion.div>
          ))}
        </motion.div>
      </div>
    );
  }
);

ImageCollage.displayName = "ImageCollage";

```

---

## `src\components\ui\image-reveal-list.tsx`

```tsx
"use client";

import React from "react";
import { cn } from "@/lib/utils";

export interface ImageRevealListItem {
  id: string;
  title: string;
  subtitle?: string;
  image: string;
  number: string;
  href?: string;
}

export interface ImageRevealListProps {
  items: ImageRevealListItem[];
  className?: string;
}

export function ImageRevealList({ items, className }: ImageRevealListProps) {
  return (
    <div className={cn("relative max-w-[500px] w-full mx-auto", className)}>
      <ul className="list-none bg-white/60 dark:bg-black/40 rounded-xl p-2 backdrop-blur-md border border-neutral-200 dark:border-white/10">
        {items.map((item) => (
          <li key={item.id} className="relative">
            <a
              href={item.href || "#"}
              className="group flex items-center p-4 text-neutral-800 dark:text-neutral-200 no-underline text-[15px] font-medium rounded-lg transition-all duration-200 hover:bg-white/90 dark:hover:bg-white/10 hover:translate-x-1"
            >
              <img
                src={item.image}
                alt={item.title}
                className="absolute -left-[100px] top-1/2 -translate-y-1/2 scale-90 w-[80px] h-[110px] rounded-md object-cover shadow-2xl opacity-0 pointer-events-none transition-all duration-300 ease-[cubic-bezier(0.34,1.56,0.64,1)] z-[100] group-hover:opacity-100 group-hover:scale-100 group-hover:-left-[90px]"
              />
              <span className="text-neutral-400 dark:text-neutral-500 text-[13px] mr-4 min-w-[24px] font-normal">
                {item.number}
              </span>
              {item.title}
              {item.subtitle && (
                <span className="ml-auto text-neutral-400 dark:text-neutral-500 text-[13px] font-normal">
                  {item.subtitle}
                </span>
              )}
            </a>
          </li>
        ))}
      </ul>
    </div>
  );
}

export default ImageRevealList;

```

---

## `src\components\ui\image-scatter.tsx`

```tsx
"use client";

import React, { useEffect, useRef } from "react";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import { cn } from "@/lib/utils";

// Register ScrollTrigger
if (typeof window !== "undefined") {
  gsap.registerPlugin(ScrollTrigger);
}

export interface ScatterSet {
  heading: string;
  images: string[];
}

export interface ImageScatterProps extends React.HTMLAttributes<HTMLDivElement> {
  data: ScatterSet[];
  cardWidth?: number;
  cardHeight?: number;
  animationDuration?: number;
  animationOverlap?: number;
  headingFadeDuration?: number;
  scroller?: string | Element | null;
}

export function ImageScatter({
  data,
  cardWidth = 250,
  cardHeight = 300,
  animationDuration = 0.75,
  animationOverlap = 0.5,
  headingFadeDuration = 0.5,
  scroller,
  className,
  ...props
}: ImageScatterProps) {
  const containerRef = useRef<HTMLDivElement>(null);
  const galleryRef = useRef<HTMLDivElement>(null);
  const headingRef = useRef<HTMLHeadingElement>(null);

  useEffect(() => {
    if (!containerRef.current || !galleryRef.current || !headingRef.current || data.length === 0) return;

    const gallery = galleryRef.current;
    const galleryHeading = headingRef.current;

    let viewport = {
      centerX: containerRef.current.clientWidth / 2,
      centerY: containerRef.current.clientHeight / 2,
      rangeMin: Math.min(containerRef.current.clientWidth, containerRef.current.clientHeight) * 0.35,
      rangeMax: Math.min(containerRef.current.clientWidth, containerRef.current.clientHeight) * 0.7,
    };

    let state = {
      activeCards: [] as { element: HTMLDivElement; centerX: number; centerY: number }[],
      currentSection: 0,
      isAnimating: false,
    };

    function updateViewport() {
      if (!containerRef.current) return;
      viewport.centerX = containerRef.current.clientWidth / 2;
      viewport.centerY = containerRef.current.clientHeight / 2;
      viewport.rangeMin = Math.min(containerRef.current.clientWidth, containerRef.current.clientHeight) * 0.35;
      viewport.rangeMax = Math.min(containerRef.current.clientWidth, containerRef.current.clientHeight) * 0.7;
    }

    function getEdgePosition(centerX: number, centerY: number) {
      const containerWidth = containerRef.current?.clientWidth || window.innerWidth;
      const containerHeight = containerRef.current?.clientHeight || window.innerHeight;

      const distances = {
        left: centerX,
        right: containerWidth - centerX,
        top: centerY,
        bottom: containerHeight - centerY,
      };

      const minDistance = Math.min(...Object.values(distances));
      const cardCenterOffsetX = cardWidth / 2;
      const cardCenterOffsetY = cardHeight / 2;
      const offsetVariation = () => (Math.random() - 0.5) * 400;

      if (minDistance === distances.left) {
        return {
          x: -cardWidth - 100 - Math.random() * 200,
          y: centerY - cardCenterOffsetY + offsetVariation(),
        };
      }
      if (minDistance === distances.right) {
        return {
          x: containerWidth + 50 + Math.random() * 200,
          y: centerY - cardCenterOffsetY + offsetVariation(),
        };
      }
      if (minDistance === distances.top) {
        return {
          x: centerX - cardCenterOffsetX + offsetVariation(),
          y: -cardHeight - 100 - Math.random() * 200,
        };
      }

      return {
        x: centerX - cardCenterOffsetX + offsetVariation(),
        y: containerHeight + 50 + Math.random() * 200,
      };
    }

    function createCards(sectionIndex: number) {
      const cards: { element: HTMLDivElement; centerX: number; centerY: number }[] = [];
      const sectionData = data[sectionIndex];
      
      if (!sectionData || !sectionData.images.length) return cards;

      sectionData.images.forEach((src) => {
        const card = document.createElement("div");
        card.className = "absolute rounded-2xl border-8 border-white dark:border-neutral-800 shadow-xl overflow-hidden will-change-transform";
        card.style.width = `${cardWidth}px`;
        card.style.height = `${cardHeight}px`;

        const img = document.createElement("img");
        img.src = src;
        img.className = "w-full h-full object-cover rounded-lg pointer-events-none";
        card.appendChild(img);

        const angle = Math.random() * Math.PI * 2;
        const radius = viewport.rangeMin + Math.random() * (viewport.rangeMax - viewport.rangeMin);
        const centerX = viewport.centerX + Math.cos(angle) * radius;
        const centerY = viewport.centerY + Math.sin(angle) * radius;

        gsap.set(card, {
          left: centerX - cardWidth / 2,
          top: centerY - cardHeight / 2,
          rotation: Math.random() * 50 - 25,
        });

        gallery.appendChild(card);
        cards.push({ element: card, centerX, centerY });
      });

      return cards;
    }

    function animateHeading(newText: string) {
      return gsap
        .timeline()
        .to(galleryHeading, {
          opacity: 0,
          duration: headingFadeDuration,
          ease: "power2.inOut",
        })
        .call(() => {
          galleryHeading.textContent = newText;
        })
        .to(galleryHeading, {
          opacity: 1,
          duration: headingFadeDuration,
          ease: "power2.inOut",
        });
    }

    function animateCards(
      exitingCards: { element: HTMLDivElement; centerX: number; centerY: number }[],
      enteringCards: { element: HTMLDivElement; centerX: number; centerY: number }[]
    ) {
      const tl = gsap.timeline();

      exitingCards.forEach(({ element, centerX, centerY }) => {
        const targetEdge = getEdgePosition(centerX, centerY);
        tl.to(
          element,
          {
            left: targetEdge.x,
            top: targetEdge.y,
            rotation: Math.random() * 180 - 90,
            duration: animationDuration,
            ease: "power2.in",
            onComplete: () => element.remove(),
          },
          0
        );
      });

      enteringCards.forEach(({ element, centerX, centerY }) => {
        const targetEdge = getEdgePosition(centerX, centerY);
        gsap.set(element, {
          left: targetEdge.x,
          top: targetEdge.y,
          rotation: Math.random() * 180 - 90,
        });

        tl.to(
          element,
          {
            left: centerX - cardWidth / 2,
            top: centerY - cardHeight / 2,
            rotation: Math.random() * 50 - 25,
            duration: animationDuration,
            ease: "power2.out",
          },
          animationOverlap
        );
      });

      return tl;
    }

    function getSectionIndex(progress: number) {
      const totalSections = data.length;
      const sectionProgress = Math.min(progress * totalSections, totalSections - 0.01);
      return Math.floor(sectionProgress);
    }

    function reinitialize() {
      state.activeCards.forEach(({ element }) => element.remove());
      updateViewport();
      state.activeCards = createCards(state.currentSection);
    }

    // Initialize first section
    state.activeCards = createCards(0);
    galleryHeading.textContent = data[0]?.heading || "";
    gsap.set(galleryHeading, { opacity: 1 });

    let intervalId: NodeJS.Timeout;

    function nextSection() {
      if (state.isAnimating) return;

      const targetSection = (state.currentSection + 1) % data.length;

      state.isAnimating = true;
      const newCards = createCards(targetSection);

      Promise.all([
        animateCards(state.activeCards, newCards).then(),
        animateHeading(data[targetSection]?.heading || "").then(),
      ]).then(() => {
        state.activeCards = newCards;
        state.currentSection = targetSection;
        state.isAnimating = false;
      });
    }

    intervalId = setInterval(nextSection, 3000); // Auto-play every 3s

    const handleResize = () => {
      reinitialize();
    };

    window.addEventListener("resize", handleResize);

    return () => {
      window.removeEventListener("resize", handleResize);
      clearInterval(intervalId);
      state.activeCards.forEach(({ element }) => element.remove());
    };
  }, [data, cardWidth, cardHeight, animationDuration, animationOverlap, headingFadeDuration]);

  return (
    <section 
      ref={containerRef}
      className={cn("relative w-full h-full flex justify-center items-center overflow-hidden bg-transparent", className)}
      {...props}
    >
      <div ref={galleryRef} className="absolute inset-0 pointer-events-none" />
      <h1 
        ref={headingRef}
        className="w-[90%] md:w-[45%] text-center text-4xl md:text-5xl lg:text-7xl font-serif font-medium leading-tight tracking-tight z-10 will-change-[opacity] text-neutral-900 dark:text-white"
      />
    </section>
  );
}

```

---

## `src\components\ui\image-trail.tsx`

```tsx
"use client";

import * as React from "react";
import { AnimatePresence, motion, type Transition } from "framer-motion";
import { cn } from "@/lib/utils";

export interface ImageTrailImage {
  src: string;
  alt?: string;
}

export interface ImageTrailProps extends React.HTMLAttributes<HTMLDivElement> {
  images?: Array<string | ImageTrailImage>;
  threshold?: number;
  minDelay?: number;
  duration?: number;
  maxItems?: number;
  rotationRange?: number;
  imageClassName?: string;
  overlayClassName?: string;
  transition?: Transition;
  exitTransition?: Transition;
  disabled?: boolean;
}

interface TrailItem {
  id: string;
  x: number;
  y: number;
  src: string;
  alt: string;
  rotation: number;
}

const DEFAULT_IMAGES: ImageTrailImage[] = [
  {
    src: "https://images.unsplash.com/photo-1617643472854-39a9dcc34eee?auto=format&fit=crop&w=480&q=80",
    alt: "Abstract architectural detail",
  },
  {
    src: "https://images.unsplash.com/photo-1674914053928-3131b730916c?auto=format&fit=crop&w=480&q=80",
    alt: "Abstract sculptural form",
  },
  {
    src: "https://images.unsplash.com/photo-1549800076-831d7a97afac?auto=format&fit=crop&w=480&q=80",
    alt: "Textured abstract composition",
  },
  {
    src: "https://images.unsplash.com/photo-1730750374142-557f2417db3a?auto=format&fit=crop&w=480&q=80",
    alt: "Minimal abstract artwork",
  },
];

const DEFAULT_TRANSITION: Transition = {
  type: "spring",
  stiffness: 260,
  damping: 22,
};

const DEFAULT_EXIT_TRANSITION: Transition = {
  duration: 0.4,
  ease: "easeInOut",
};

const normalizeImage = (image: string | ImageTrailImage): ImageTrailImage =>
  typeof image === "string" ? { src: image, alt: "" } : image;

export function ImageTrail({
  images = DEFAULT_IMAGES,
  threshold = 80,
  minDelay = 50,
  duration = 1000,
  maxItems = 8,
  rotationRange = 40,
  imageClassName,
  overlayClassName,
  transition = DEFAULT_TRANSITION,
  exitTransition = DEFAULT_EXIT_TRANSITION,
  disabled = false,
  className,
  children,
  onPointerMove,
  onPointerLeave,
  ...props
}: ImageTrailProps) {
  const [trail, setTrail] = React.useState<TrailItem[]>([]);
  const lastPositionRef = React.useRef<{ x: number; y: number } | null>(null);
  const lastTimeRef = React.useRef(0);
  const imageIndexRef = React.useRef(0);
  const timeoutRefs = React.useRef<Set<number>>(new Set());
  const normalizedImages = React.useMemo(() => images.map(normalizeImage), [images]);
  const safeThreshold = Math.max(0, threshold);
  const safeMinDelay = Math.max(0, minDelay);
  const safeDuration = Math.max(0, duration);
  const safeMaxItems = Math.max(0, Math.floor(maxItems));
  const safeRotationRange = Math.max(0, rotationRange);

  React.useEffect(() => {
    const timeouts = timeoutRefs.current;

    return () => {
      timeouts.forEach((timeout) => window.clearTimeout(timeout));
      timeouts.clear();
    };
  }, []);

  const removeItem = React.useCallback((id: string) => {
    setTrail((current) => current.filter((item) => item.id !== id));
  }, []);

  const handlePointerMove = React.useCallback(
    (event: React.PointerEvent<HTMLDivElement>) => {
      onPointerMove?.(event);

      if (disabled || !normalizedImages.length || safeMaxItems === 0) {
        return;
      }

      const bounds = event.currentTarget.getBoundingClientRect();
      const position = {
        x: event.clientX - bounds.left,
        y: event.clientY - bounds.top,
      };
      const lastPosition = lastPositionRef.current;
      const now = window.performance.now();

      if (lastPosition) {
        const distance = Math.hypot(position.x - lastPosition.x, position.y - lastPosition.y);

        if (distance < safeThreshold || now - lastTimeRef.current < safeMinDelay) {
          return;
        }
      }

      const image = normalizedImages[imageIndexRef.current % normalizedImages.length];
      const id = `${Math.round(now)}-${Math.random().toString(36).slice(2)}`;
      const rotation = Math.random() * safeRotationRange - safeRotationRange / 2;

      imageIndexRef.current = (imageIndexRef.current + 1) % normalizedImages.length;
      lastPositionRef.current = position;
      lastTimeRef.current = now;

      setTrail((current) => {
        const next = [
          ...current,
          {
            id,
            x: position.x,
            y: position.y,
            src: image.src,
            alt: image.alt ?? "",
            rotation,
          },
        ];

        return next.slice(Math.max(0, next.length - safeMaxItems));
      });

      const timeout = window.setTimeout(() => {
        timeoutRefs.current.delete(timeout);
        removeItem(id);
      }, safeDuration);
      timeoutRefs.current.add(timeout);
    },
    [
      disabled,
      normalizedImages,
      onPointerMove,
      removeItem,
      safeDuration,
      safeMaxItems,
      safeMinDelay,
      safeRotationRange,
      safeThreshold,
    ]
  );

  const handlePointerLeave = React.useCallback(
    (event: React.PointerEvent<HTMLDivElement>) => {
      lastPositionRef.current = null;
      onPointerLeave?.(event);
    },
    [onPointerLeave]
  );

  return (
    <div
      className={cn("relative overflow-hidden", className)}
      onPointerMove={handlePointerMove}
      onPointerLeave={handlePointerLeave}
      {...props}
    >
      {children}

      <div className={cn("pointer-events-none absolute inset-0 z-50 overflow-hidden", overlayClassName)}>
        <AnimatePresence>
          {trail.map((item) => (
            <motion.div
              key={item.id}
              className="absolute"
              style={{ left: item.x, top: item.y }}
              initial={{ x: "-50%", y: "-50%", scale: 0.82, opacity: 0, rotate: item.rotation }}
              animate={{ x: "-50%", y: "-50%", scale: 1, opacity: 1, rotate: item.rotation }}
              exit={{
                x: "-50%",
                y: "-50%",
                scale: 0.5,
                opacity: 0,
                rotate: item.rotation * 0.75,
                transition: exitTransition,
              }}
              transition={transition}
            >
              {/* eslint-disable-next-line @next/next/no-img-element */}
              <img
                src={item.src}
                alt={item.alt}
                draggable={false}
                className={cn(
                  "block aspect-[3/4] w-32 select-none rounded-sm object-cover shadow-2xl",
                  imageClassName
                )}
              />
            </motion.div>
          ))}
        </AnimatePresence>
      </div>
    </div>
  );
}

export default ImageTrail;

```

---

## `src\components\ui\input-group.tsx`

```tsx
"use client"

import * as React from "react"
import { cva, type VariantProps } from "class-variance-authority"

import { cn } from "@/lib/utils"
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"
import { Textarea } from "@/components/ui/textarea"

function InputGroup({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="input-group"
      role="group"
      className={cn(
        "group/input-group border-input dark:bg-input/30 relative flex w-full items-center rounded-md border shadow-xs transition-[color,box-shadow] outline-none",
        "h-9 min-w-0 has-[>textarea]:h-auto",

        // Variants based on alignment.
        "has-[>[data-align=inline-start]]:[&>input]:pl-2",
        "has-[>[data-align=inline-end]]:[&>input]:pr-2",
        "has-[>[data-align=block-start]]:h-auto has-[>[data-align=block-start]]:flex-col has-[>[data-align=block-start]]:[&>input]:pb-3",
        "has-[>[data-align=block-end]]:h-auto has-[>[data-align=block-end]]:flex-col has-[>[data-align=block-end]]:[&>input]:pt-3",

        // Focus state.
        "has-[[data-slot=input-group-control]:focus-visible]:border-ring has-[[data-slot=input-group-control]:focus-visible]:ring-ring/50 has-[[data-slot=input-group-control]:focus-visible]:ring-[3px]",

        // Error state.
        "has-[[data-slot][aria-invalid=true]]:ring-destructive/20 has-[[data-slot][aria-invalid=true]]:border-destructive dark:has-[[data-slot][aria-invalid=true]]:ring-destructive/40",

        className
      )}
      {...props}
    />
  )
}

const inputGroupAddonVariants = cva(
  "text-muted-foreground flex h-auto cursor-text items-center justify-center gap-2 py-1.5 text-sm font-medium select-none [&>svg:not([class*='size-'])]:size-4 [&>kbd]:rounded-[calc(var(--radius)-5px)] group-data-[disabled=true]/input-group:opacity-50",
  {
    variants: {
      align: {
        "inline-start":
          "order-first pl-3 has-[>button]:ml-[-0.45rem] has-[>kbd]:ml-[-0.35rem]",
        "inline-end":
          "order-last pr-3 has-[>button]:mr-[-0.45rem] has-[>kbd]:mr-[-0.35rem]",
        "block-start":
          "order-first w-full justify-start px-3 pt-3 [.border-b]:pb-3 group-has-[>input]/input-group:pt-2.5",
        "block-end":
          "order-last w-full justify-start px-3 pb-3 [.border-t]:pt-3 group-has-[>input]/input-group:pb-2.5",
      },
    },
    defaultVariants: {
      align: "inline-start",
    },
  }
)

function InputGroupAddon({
  className,
  align = "inline-start",
  ...props
}: React.ComponentProps<"div"> & VariantProps<typeof inputGroupAddonVariants>) {
  return (
    <div
      role="group"
      data-slot="input-group-addon"
      data-align={align}
      className={cn(inputGroupAddonVariants({ align }), className)}
      onClick={(e) => {
        if ((e.target as HTMLElement).closest("button")) {
          return
        }
        e.currentTarget.parentElement?.querySelector("input")?.focus()
      }}
      {...props}
    />
  )
}

const inputGroupButtonVariants = cva(
  "text-sm shadow-none flex gap-2 items-center",
  {
    variants: {
      size: {
        xs: "h-6 gap-1 px-2 rounded-[calc(var(--radius)-5px)] [&>svg:not([class*='size-'])]:size-3.5 has-[>svg]:px-2",
        sm: "h-8 px-2.5 gap-1.5 rounded-md has-[>svg]:px-2.5",
        "icon-xs":
          "size-6 rounded-[calc(var(--radius)-5px)] p-0 has-[>svg]:p-0",
        "icon-sm": "size-8 p-0 has-[>svg]:p-0",
      },
    },
    defaultVariants: {
      size: "xs",
    },
  }
)

function InputGroupButton({
  className,
  type = "button",
  variant = "ghost",
  size = "xs",
  ...props
}: Omit<React.ComponentProps<typeof Button>, "size"> &
  VariantProps<typeof inputGroupButtonVariants>) {
  return (
    <Button
      type={type}
      data-size={size}
      variant={variant}
      className={cn(inputGroupButtonVariants({ size }), className)}
      {...props}
    />
  )
}

function InputGroupText({ className, ...props }: React.ComponentProps<"span">) {
  return (
    <span
      className={cn(
        "text-muted-foreground flex items-center gap-2 text-sm [&_svg]:pointer-events-none [&_svg:not([class*='size-'])]:size-4",
        className
      )}
      {...props}
    />
  )
}

function InputGroupInput({
  className,
  ...props
}: React.ComponentProps<"input">) {
  return (
    <Input
      data-slot="input-group-control"
      className={cn(
        "flex-1 rounded-none border-0 bg-transparent shadow-none focus-visible:ring-0 dark:bg-transparent",
        className
      )}
      {...props}
    />
  )
}

function InputGroupTextarea({
  className,
  ...props
}: React.ComponentProps<"textarea">) {
  return (
    <Textarea
      data-slot="input-group-control"
      className={cn(
        "flex-1 resize-none rounded-none border-0 bg-transparent py-3 shadow-none focus-visible:ring-0 dark:bg-transparent",
        className
      )}
      {...props}
    />
  )
}

export {
  InputGroup,
  InputGroupAddon,
  InputGroupButton,
  InputGroupText,
  InputGroupInput,
  InputGroupTextarea,
}

```

---

## `src\components\ui\input-otp.tsx`

```tsx
"use client"

import * as React from "react"
import { OTPInput, OTPInputContext } from "input-otp"
import { MinusIcon } from "lucide-react"

import { cn } from "@/lib/utils"

function InputOTP({
  className,
  containerClassName,
  ...props
}: React.ComponentProps<typeof OTPInput> & {
  containerClassName?: string
}) {
  return (
    <OTPInput
      data-slot="input-otp"
      containerClassName={cn(
        "flex items-center gap-2 has-disabled:opacity-50",
        containerClassName
      )}
      className={cn("disabled:cursor-not-allowed", className)}
      {...props}
    />
  )
}

function InputOTPGroup({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="input-otp-group"
      className={cn("flex items-center", className)}
      {...props}
    />
  )
}

function InputOTPSlot({
  index,
  className,
  ...props
}: React.ComponentProps<"div"> & {
  index: number
}) {
  const inputOTPContext = React.useContext(OTPInputContext)
  const { char, hasFakeCaret, isActive } = inputOTPContext?.slots[index] ?? {}

  return (
    <div
      data-slot="input-otp-slot"
      data-active={isActive}
      className={cn(
        "data-[active=true]:border-ring data-[active=true]:ring-ring/50 data-[active=true]:aria-invalid:ring-destructive/20 dark:data-[active=true]:aria-invalid:ring-destructive/40 aria-invalid:border-destructive data-[active=true]:aria-invalid:border-destructive dark:bg-input/30 border-input relative flex h-9 w-9 items-center justify-center border-y border-r text-sm shadow-xs transition-all outline-none first:rounded-l-md first:border-l last:rounded-r-md data-[active=true]:z-10 data-[active=true]:ring-[3px]",
        className
      )}
      {...props}
    >
      {char}
      {hasFakeCaret && (
        <div className="pointer-events-none absolute inset-0 flex items-center justify-center">
          <div className="animate-caret-blink bg-foreground h-4 w-px duration-1000" />
        </div>
      )}
    </div>
  )
}

function InputOTPSeparator({ ...props }: React.ComponentProps<"div">) {
  return (
    <div data-slot="input-otp-separator" role="separator" {...props}>
      <MinusIcon />
    </div>
  )
}

export { InputOTP, InputOTPGroup, InputOTPSlot, InputOTPSeparator }

```

---

## `src\components\ui\input.tsx`

```tsx
import * as React from 'react'

import { cn } from '@/lib/utils'

function Input({ className, type, ...props }: React.ComponentProps<'input'>) {
    return (
        <input
            type={type}
            data-slot="input"
            className={cn(
                'border-input file:text-foreground placeholder:text-muted-foreground selection:bg-primary selection:text-primary-foreground shadow-xs flex h-9 w-full min-w-0 rounded-md border bg-transparent px-3 py-1 text-base outline-none transition-[color,box-shadow] file:inline-flex file:h-7 file:border-0 file:bg-transparent file:text-sm file:font-medium disabled:pointer-events-none disabled:cursor-not-allowed disabled:opacity-50 md:text-sm',
                'focus-visible:border-ring focus-visible:ring-ring/50 focus-visible:ring-[3px]',
                'aria-invalid:ring-destructive/20 dark:aria-invalid:ring-destructive/40 aria-invalid:border-destructive',
                className
            )}
            {...props}
        />
    )
}

export { Input }

```

---

## `src\components\ui\interactive-book.tsx`

```tsx
"use client";

import React, { useState, useEffect } from 'react';
import { motion, AnimatePresence } from 'framer-motion';
import { cn } from '@/lib/utils';
import { ChevronLeft, ChevronRight, RefreshCcw, X, BookOpen } from 'lucide-react';

export interface BookPage {
    title?: string;
    content: React.ReactNode;
    backContent?: React.ReactNode;
    pageNumber: number;
}

export interface InteractiveBookProps {
    coverImage: string;
    bookTitle?: string;
    bookAuthor?: string;
    pages: BookPage[];
    className?: string;
    width?: number | string;
    height?: number | string;
}

export default function InteractiveBook({
    coverImage,
    bookTitle = "Book Title",
    bookAuthor = "Author Name",
    pages,
    className,
    width = 350,
    height = 500,
}: InteractiveBookProps) {
    const [isOpen, setIsOpen] = useState(false);
    const [currentPageIndex, setCurrentPageIndex] = useState(-1);
    const [isHovering, setIsHovering] = useState(false);

    // Calculate dynamic width/height values for animations
    const widthNum = typeof width === 'number' ? width : 350;

    // Sync container shift with cover open
    const BOOK_OPEN_DURATION = 1.5;
    const EASING: [number, number, number, number] = [0.25, 0, 0, 1]; // milder smoothing

    const handleOpenBook = () => setIsOpen(true);

    const handleCloseBook = (e?: React.MouseEvent) => {
        e?.stopPropagation();
        setIsOpen(false);
        setCurrentPageIndex(-1);
    };

    const nextPage = (e?: React.MouseEvent) => {
        e?.stopPropagation();
        if (currentPageIndex < pages.length - 1) {
            setCurrentPageIndex((prev) => prev + 1);
        }
    };

    const prevPage = (e?: React.MouseEvent) => {
        e?.stopPropagation();
        if (currentPageIndex >= 0) {
            setCurrentPageIndex((prev) => prev - 1);
        }
    };

    const restartBook = (e?: React.MouseEvent) => {
        e?.stopPropagation();
        setCurrentPageIndex(-1);
    };

    const handleSliderChange = (e: React.ChangeEvent<HTMLInputElement>) => {
        setCurrentPageIndex(parseInt(e.target.value, 10));
    };

    // Keyboard navigation
    useEffect(() => {
        if (!isOpen) return;
        const handleKeyDown = (e: KeyboardEvent) => {
            if (e.key === 'ArrowRight') nextPage();
            if (e.key === 'ArrowLeft') prevPage();
            if (e.key === 'Escape') handleCloseBook();
        };
        window.addEventListener('keydown', handleKeyDown);
        return () => window.removeEventListener('keydown', handleKeyDown);
    }, [isOpen, currentPageIndex]);

    return (
        <div
            className={cn("relative flex items-center justify-center perspective-[2000px]", className)}
            style={{
                width: typeof width === 'number' ? width * 3.5 : '100%',
                height: typeof height === 'number' ? height + 100 : 'auto'
            }}
        >
            <motion.div
                className={cn(
                    "relative preserve-3d"
                )}
                style={{ width, height }}
                initial={{ x: 0 }}
                animate={{ x: isOpen ? widthNum / 2 : 0 }}
                transition={{ duration: BOOK_OPEN_DURATION, ease: EASING }}
            >

                {/* Front Cover */}
                <motion.div
                    className="absolute inset-0 w-full h-full origin-left"
                    initial={{ rotateY: 0, zIndex: 100 }}
                    animate={{
                        rotateY: isOpen ? -180 : (isHovering ? -15 : 0),
                        zIndex: isOpen ? 0 : 100
                    }}
                    transition={{
                        rotateY: { duration: BOOK_OPEN_DURATION, ease: EASING },
                        zIndex: { delay: isOpen ? BOOK_OPEN_DURATION * 0.6 : BOOK_OPEN_DURATION * 0.4 }
                    }}
                    style={{ transformStyle: 'preserve-3d' }}
                    onClick={!isOpen ? handleOpenBook : undefined}
                    onHoverStart={() => !isOpen && setIsHovering(true)}
                    onHoverEnd={() => setIsHovering(false)}
                >
                    {/* Front Face */}
                    <div
                        className="absolute inset-0 w-full h-full backface-hidden rounded-r-md rounded-l-sm shadow-2xl cursor-pointer overflow-hidden group"
                        style={{ transform: 'translateZ(0.5px)' }}
                    >
                        {/* Image Background */}
                        <div
                            className="absolute inset-0 bg-cover bg-center transition-transform duration-700 group-hover:scale-105"
                            style={{ backgroundImage: `url(${coverImage})` }}
                        />
                        <div className="absolute inset-0 bg-gradient-to-t from-black/80 via-black/20 to-transparent" />

                        <div className="absolute bottom-4 left-3 right-3 text-white text-left">
                            <h1 className="text-sm font-serif font-bold tracking-wide mb-1 drop-shadow-md leading-tight">{bookTitle}</h1>
                            <p className="text-[8px] font-sans tracking-widest opacity-90 uppercase border-t border-white/30 pt-1 inline-block">{bookAuthor}</p>
                        </div>

                        {/* Spine Highlight */}
                        <div className="absolute left-0 top-0 bottom-0 w-4 bg-gradient-to-r from-white/30 to-transparent opacity-40" />
                        <div className="absolute left-[12px] top-0 bottom-0 w-[1px] bg-black/30" />
                    </div>

                    {/* Back Face (Inner Cover) */}
                    <div
                        className="absolute inset-0 w-full h-full backface-hidden rounded-l-md rounded-r-sm bg-[#fdfbf7] rotate-y-180 flex flex-col p-8 border-r border-neutral-200 shadow-xl cursor-pointer hover:bg-[#fcfaf5] transition-colors"
                        style={{ transform: 'rotateY(180deg) translateZ(0.5px)' }}
                        onClick={(e) => {
                            e.stopPropagation();
                            prevPage();
                        }}
                    >
                        <div className="flex-1 flex flex-col justify-center items-center text-center opacity-80">
                            <h2 className="text-2xl font-serif text-neutral-800 mb-2 tracking-wide">{bookTitle}</h2>
                            <div className="w-8 h-[1px] bg-neutral-300 mb-3" />
                            <p className="text-xs text-neutral-500 uppercase tracking-widest">Interactive Edition</p>
                        </div>
                    </div>
                </motion.div>

                {/* Pages Stack */}
                <div className="absolute inset-0 w-full h-full z-0" style={{ transformStyle: 'preserve-3d' }}>
                    {pages.map((page, index) => {
                        const isFlipped = index <= currentPageIndex;
                        // Stagger delays slightly for a realistic "whip" effect if user clicks fast, 
                        // but mostly we want instant feedback with smooth transition.

                        return (
                            <motion.div
                                key={index}
                                className="absolute inset-0 w-full h-full origin-left bg-[#fdfbf7] rounded-r-md rounded-l-sm shadow-sm border border-neutral-100"
                                style={{ transformStyle: 'preserve-3d' }}
                                initial={{ rotateY: 0, zIndex: pages.length - index }}
                                animate={{
                                    rotateY: isFlipped ? -180 : 0,
                                    zIndex: isFlipped ? index + 1 : pages.length - index
                                }}
                                transition={{
                                    duration: 0.6,
                                    ease: [0.645, 0.045, 0.355, 1]
                                }}
                            >
                                {/* Front Face (Right Side) */}
                                <div
                                    className="absolute inset-0 w-full h-full backface-hidden p-8 flex flex-col bg-[#fdfbf7] cursor-pointer hover:bg-[#fcfaf5] transition-colors"
                                    style={{ transform: 'translateZ(0.5px)' }}
                                    onClick={(e) => {
                                        e.stopPropagation();
                                        nextPage();
                                    }}
                                >
                                    <div className="flex-1">
                                        <div className="text-xs text-neutral-400 text-right mb-4 font-sans tracking-wider">
                                            {page.pageNumber * 2 - 1}
                                        </div>
                                        <div className="prose prose-neutral prose-sm max-w-none font-serif text-neutral-700 leading-relaxed select-none">
                                            {page.title && (
                                                <h3 className="text-xl font-medium text-center mb-6 text-neutral-800 tracking-tight">
                                                    {page.title}
                                                </h3>
                                            )}
                                            {page.content}
                                        </div>
                                    </div>
                                    <div className="absolute left-0 top-0 bottom-0 w-8 bg-gradient-to-r from-black/5 to-transparent pointer-events-none mix-blend-multiply" />
                                </div>

                                {/* Back Face (Left Side) */}
                                <div
                                    className="absolute inset-0 w-full h-full backface-hidden rotate-y-180 bg-[#fdfbf7] border-r border-neutral-200 overflow-hidden p-8 flex flex-col cursor-pointer hover:bg-[#fcfaf5] transition-colors"
                                    style={{ transform: 'rotateY(180deg) translateZ(0.5px)' }}
                                    onClick={(e) => {
                                        e.stopPropagation();
                                        prevPage();
                                    }}
                                >
                                    <div className="absolute right-0 top-0 bottom-0 w-8 bg-gradient-to-l from-black/5 to-transparent pointer-events-none mix-blend-multiply" />

                                    <div className="flex-1 overflow-hidden">
                                        <div className="text-xs text-neutral-400 text-left mb-4 font-sans tracking-wider">
                                            {page.pageNumber * 2}
                                        </div>
                                        <div className="prose prose-neutral prose-sm max-w-none font-serif text-neutral-700 leading-relaxed select-none h-full flex flex-col">
                                            {page.backContent ? (
                                                <div className="flex-1">
                                                    {page.backContent}
                                                </div>
                                            ) : (
                                                <div className="w-full h-full flex items-center justify-center opacity-[0.03]">
                                                    <span className="font-serif text-8xl italic font-bold text-black">
                                                        {page.pageNumber * 2}
                                                    </span>
                                                </div>
                                            )}
                                        </div>
                                    </div>
                                </div>
                            </motion.div>
                        );
                    })}

                    {/* Back Cover (Static) */}
                    <div
                        className="absolute inset-0 w-full h-full bg-[#fdfbf7] rounded-r-md rounded-l-sm shadow-xl border border-neutral-200"
                        style={{ transform: 'translateZ(-1px)', zIndex: -1 }}
                    >
                        <div className="absolute inset-0 p-8 flex flex-col items-center justify-center text-center opacity-40">
                            <p className="font-serif text-neutral-500 italic">The End</p>
                            <button
                                onClick={restartBook}
                                className="mt-4 flex items-center gap-2 px-4 py-2 rounded-full bg-neutral-100 hover:bg-neutral-200 transition-colors text-sm text-neutral-600 cursor-pointer"
                            >
                                <RefreshCcw size={14} /> Read Again
                            </button>
                        </div>
                    </div>
                </div>

                {/* Controls Bar Removed */}

            </motion.div>

            {/* Side Navigation Arrows */}
            <AnimatePresence>
                {isOpen && (
                    <>
                        {/* Close Button */}
                        <motion.button
                            initial={{ opacity: 0, scale: 0.8 }}
                            animate={{ opacity: 1, scale: 1 }}
                            exit={{ opacity: 0, scale: 0.8 }}
                            onClick={handleCloseBook}
                            className="absolute top-8 right-8 p-2 rounded-full bg-white/50 dark:bg-neutral-800/50 hover:bg-white dark:hover:bg-neutral-800 border border-transparent hover:border-neutral-200 dark:hover:border-neutral-700 backdrop-blur-sm text-neutral-800 dark:text-neutral-100 z-[1000] transition-all hover:scale-110 shadow-sm hover:shadow-xl"
                        >
                            <X size={24} />
                        </motion.button>
                    </>
                )}
            </AnimatePresence>

            {/* Hint */}
            {!isOpen && (
                <motion.div
                    initial={{ opacity: 0, y: 10 }}
                    animate={{ opacity: 1, y: 0 }}
                    transition={{ delay: 1, duration: 1 }}
                    className="absolute bottom-4 text-neutral-500 dark:text-neutral-400 text-sm font-medium tracking-widest uppercase cursor-pointer z-50 hover:text-neutral-700 dark:hover:text-neutral-200 transition-colors"
                    onClick={handleOpenBook}
                >
                    Click to Open
                </motion.div>
            )}
        </div>
    );
}

```

---

## `src\components\ui\interactive-hover-button.tsx`

```tsx
import { ArrowRight } from "lucide-react"

import { cn } from "@/lib/utils"

export function InteractiveHoverButton({
  children,
  className,
  ...props
}: React.ButtonHTMLAttributes<HTMLButtonElement>) {
  return (
    <button
      className={cn(
        "group bg-background relative w-auto cursor-pointer overflow-hidden rounded-full border p-2 px-6 text-center font-semibold",
        className
      )}
      {...props}
    >
      <div className="flex items-center gap-2">
        <div className="bg-primary h-2 w-2 rounded-full transition-all duration-300 group-hover:scale-[100.8]"></div>
        <span className="inline-block transition-all duration-300 group-hover:translate-x-12 group-hover:opacity-0">
          {children}
        </span>
      </div>
      <div className="text-primary-foreground absolute top-0 z-10 flex h-full w-full translate-x-12 items-center justify-center gap-2 opacity-0 transition-all duration-300 group-hover:-translate-x-5 group-hover:opacity-100">
        <span>{children}</span>
        <ArrowRight />
      </div>
    </button>
  )
}

```

---

## `src\components\ui\interactive-keyboard.tsx`

```tsx
"use client";

import React, { useState, useEffect } from "react";
import { cn } from "@/lib/utils";

export interface InteractiveKeyboardProps extends Omit<React.HTMLAttributes<HTMLDivElement>, 'onKeyPress'> {
  onKeyClick?: (key: string) => void;
}

export function InteractiveKeyboard({ className, onKeyClick, ...props }: InteractiveKeyboardProps) {
  const [capsLock, setCapsLock] = useState(false);

  const handleKeyClick = (keyName: string) => {
    if (keyName === "caps lock") {
      setCapsLock(!capsLock);
    }
    onKeyClick?.(keyName);
  };

  return (
    <div className={cn("interactive-keyboard-wrapper", className)} {...props}>
      <style>{`
        .interactive-keyboard-wrapper {
          --kb-bg: hsl(0,0%,93%);
          --kb-text: hsl(0,0%,47%);
          --kb-shadow-1: hsla(0,0%,0%,0.25);
          --kb-shadow-2: hsla(0,0%,0%,0.3);
          --kb-shadow-3: hsla(0,0%,0%,0.4);
          --kb-shadow-4: hsla(0,0%,100%,0.8);
          --kb-active-shadow-1: hsla(0,0%,0%,0.2);
          --kb-active-shadow-2: hsla(0,0%,0%,0.4);
          --kb-focus-bg: hsl(0,0%,100%);
          --kb-focus-text: hsl(0,0%,54%);
          
          font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen-Sans, Ubuntu, Cantarell, "Helvetica Neue", sans-serif;
          display: flex;
          width: 100%;
          justify-content: center;
          padding: 4em;
        }

        :is(.dark) .interactive-keyboard-wrapper {
          --kb-bg: #1a1a1c;
          --kb-text: #909096;
          --kb-shadow-1: rgba(0,0,0,0.8);
          --kb-shadow-2: rgba(0,0,0,0.4);
          --kb-shadow-3: rgba(0,0,0,0.7);
          --kb-shadow-4: rgba(255,255,255,0.06);
          --kb-active-shadow-1: rgba(0,0,0,0.9);
          --kb-active-shadow-2: rgba(0,0,0,0.6);
          --kb-focus-bg: #222224;
          --kb-focus-text: #ffffff;
        }

        .interactive-keyboard-wrapper button {
          background-color: var(--kb-bg);
          border-radius: 0.125em;
          box-shadow:
            -0.2em -0.125em 0.125em var(--kb-shadow-1),
            0 0 0 0.04em var(--kb-shadow-2),
            0.02em 0.02em 0.02em var(--kb-shadow-3) inset,
            -0.05em -0.05em 0.02em var(--kb-shadow-4) inset;
          color: var(--kb-text);
          display: block;
          font-size: 1em;
          outline: transparent;
          position: relative;
          -webkit-appearance: none;
          appearance: none;
          -webkit-tap-highlight-color: transparent;
          user-select: none;
          border: 0;
          margin: 0;
          padding: 0;
          cursor: pointer;
        }

        .interactive-keyboard-wrapper button:active {
          box-shadow:
            0.1em 0.1em 0.1em var(--kb-active-shadow-1),
            0 0 0 0.05em var(--kb-active-shadow-2),
            -0.025em -0.05em 0.025em var(--kb-shadow-4) inset;
        }
        .interactive-keyboard-wrapper button:focus-visible {
          background-color: var(--kb-focus-bg);
          color: var(--kb-focus-text);
        }
        .interactive-keyboard-wrapper button span {
          display: inline-flex;
          flex-wrap: wrap;
          justify-content: center;
          align-items: center;
        }
        .interactive-keyboard-wrapper button > span {
          margin: auto;
          padding: 0.2em 0.375em;
          position: absolute;
          top: 50%;
          left: 0;
          font-size: 0.5em;
          line-height: 2;
          transform: translateY(-50%) scaleX(0.875);
          width: 100%;
        }
        .interactive-keyboard-wrapper button[aria-pressed="true"] .dot-light {
          color: hsl(88,100%,50%);
          text-shadow: 0 0 2px hsl(88,100%,27%);
        }

        /* Keyboard */
        .ikb-keyboard {
          background-image: linear-gradient(90deg,hsl(0,0%,53%),hsl(0,0%,80%));
          border-radius: 0.5em;
          box-shadow:
            -1em -1em 1.5em hsla(0,0%,0%,0.6),
            0 0 0 1px hsl(0,0%,67%) inset;
          display: grid;
          gap: 0.375em 0.875em;
          grid-template-columns: 21.25em 4.125em 5.65em;
          grid-template-rows: 0.75em 1.125em 1.125em 1.125em 1.125em 1.375em;
          font-size: clamp(8px, 2.5vw, 24px);
          margin: auto;
          padding: 0.25em;
          width: 33.25em;
          height: 9em;
        }

        :is(.dark) .interactive-keyboard-wrapper .ikb-keyboard {
           background-image: linear-gradient(135deg, #2a2a2c, #161618);
           box-shadow: 
             0 2em 4em -1em rgba(0,0,0,0.6),
             0 0 0 1px rgba(255,255,255,0.08) inset,
             0 1px 1px rgba(255,255,255,0.15) inset;
        }

        .ikb-row {
          display: flex;
          gap: 0.35em;
        }
        .ikb-row:nth-of-type(14) {
          margin: auto;
        }
        .ikb-row:nth-of-type(n + 14):nth-of-type(-3n + 17) {
          transform: translateY(0.25em);
        }
        .interactive-keyboard-wrapper button > span.ikb-bump {
          border-radius: 0.1em;
          box-shadow: -0.05em -0.02em 0 0.05em hsla(0,0%,0%,0.3);
          padding: 0;
          top: 85%;
          left: calc(50% - 0.4em);
          width: 0.8em;
          height: 0.15em;
          transform: none;
        }

        /* Button size */
        .ikb-btn-0 { width: 1.19em; height: 0.75em; }
        .ikb-btn-1 { width: 1.125em; height: 0.75em; }
        .ikb-btn-2 { width: 1.125em; height: 1.125em; }
        .ikb-btn-3 { width: 2em; height: 1.125em; }
        .ikb-btn-4 { width: 2.3em; height: 1.125em; }
        .ikb-btn-5 { width: 3.05em; height: 1.125em; }
        .ikb-btn-6 { width: 1.5625em; height: 1.375em; }
        .ikb-btn-7 { width: 1.8375em; height: 1.375em; }
        .ikb-btn-8 { width: 1.125em; height: 1.375em; }
        .ikb-btn-9 { width: 2.6875em; height: 1.375em; }
        .ikb-btn-10 { width: 1.125em; height: 2.875em; }
        .ikb-btn-longest { width: 8.625em; height: 1.375em; }

        /* Alignment */
        .interactive-keyboard-wrapper button > span.ikb-ul, 
        .interactive-keyboard-wrapper button > span.ikb-ll, 
        .interactive-keyboard-wrapper button > span.ikb-ur, 
        .interactive-keyboard-wrapper button > span.ikb-lr { top: 0; transform: scaleX(0.875); }
        .interactive-keyboard-wrapper button > span.ikb-ul, 
        .interactive-keyboard-wrapper button > span.ikb-ll { justify-content: flex-start; transform-origin: 0 50%; }
        .interactive-keyboard-wrapper button > span.ikb-ur, 
        .interactive-keyboard-wrapper button > span.ikb-lr { justify-content: flex-end; transform-origin: 100% 50%; }
        .interactive-keyboard-wrapper button > span.ikb-ll, 
        .interactive-keyboard-wrapper button > span.ikb-lr { top: auto; bottom: 0; }
        .interactive-keyboard-wrapper button > span.ikb-noxscale { transform: translateY(-50%) scaleX(1); }
        .interactive-keyboard-wrapper button > span.ikb-ll.ikb-noxscale, 
        .interactive-keyboard-wrapper button > span.ikb-lr.ikb-noxscale { transform: scaleX(1); }

        /* Fonts */
        .interactive-keyboard-wrapper button > span.ikb-xxxs { font-size: 0.2em; line-height: 1.5; }
        .interactive-keyboard-wrapper button > span.ikb-xxs { font-size: 0.25em; line-height: 1.5; }
        .interactive-keyboard-wrapper button > span.ikb-xs { font-size: 0.3em; line-height: 1.125; }
        .interactive-keyboard-wrapper button > span.ikb-sm { font-size: 0.4em; line-height: 1.25; }

        /* Icons */
        .ikb-up, .ikb-right, .ikb-down, .ikb-left { width: 0; height: 0; vertical-align: 0.1em; }
        .ikb-up { border-left: 0.25em solid transparent; border-right: 0.25em solid transparent; border-bottom: 0.5em solid currentColor; }
        .ikb-right { border-left: 0.5em solid currentColor; border-top: 0.25em solid transparent; border-bottom: 0.25em solid transparent; }
        .ikb-down { border-left: 0.25em solid transparent; border-right: 0.25em solid transparent; border-top: 0.5em solid currentColor; }
        .ikb-left { border-right: 0.5em solid currentColor; border-top: 0.25em solid transparent; border-bottom: 0.25em solid transparent; }
        
        .ikb-pause { border-left: 0.2em solid; border-right: 0.2em solid; vertical-align: 0.1em; width: 0.475em; height: 0.5em; }
        .ikb-emoji { filter: saturate(0); -webkit-filter: saturate(0); }
        
        .ikb-block { margin-left: 0.1em; height: 0.8em; width: 0.6em; vertical-align: 0.1em; }
        .ikb-cascade { position: relative; height: 1em; width: 1.2em; }
        .ikb-cascade:before, .ikb-cascade:after { content: ""; position: absolute; height: 0.45em; width: 0.8em; }
        .ikb-cascade:before { top: 0; left: 0; }
        .ikb-cascade:after { right: 0; bottom: 0; }
        
        .ikb-block, .ikb-cascade:before, .ikb-cascade:after { border: 1px solid; }
        .ikb-apps:before, .ikb-apps:after { font-weight: bold; display: block; content: "\\25A1\\25A1\\25A1"; line-height: 0.875; }
        
        .interactive-keyboard-wrapper button > span.ikb-noxpad { padding: 0.2em 0; }
      `}</style>
      
      <div className="ikb-keyboard">
        <div className="ikb-row">
          <button type="button" className="ikb-btn-0" onClick={() => handleKeyClick("esc")}><span className="ikb-xs">esc</span></button>
          <button type="button" className="ikb-btn-0" onClick={() => handleKeyClick("f1")}><span className="ikb-xs ikb-noxscale"><span className="ikb-emoji">&#128261;</span></span><span className="ikb-lr ikb-xxxs">F1</span></button>
          <button type="button" className="ikb-btn-0" onClick={() => handleKeyClick("f2")}><span className="ikb-xs ikb-noxscale"><span className="ikb-emoji">&#128262;</span></span><span className="ikb-lr ikb-xxxs">F2</span></button>
          <button type="button" className="ikb-btn-0" onClick={() => handleKeyClick("f3")}><span className="ikb-xs ikb-noxscale"><span className="ikb-cascade"></span><span className="ikb-block"></span></span><span className="ikb-lr ikb-xxxs">F3</span></button>
          <button type="button" className="ikb-btn-0" onClick={() => handleKeyClick("f4")}><span className="ikb-xxxs ikb-noxscale"><span className="ikb-apps"></span></span><span className="ikb-lr ikb-xxxs">F4</span></button>
          <button type="button" className="ikb-btn-0" onClick={() => handleKeyClick("f5")}><span className="ikb-lr ikb-xxxs">F5</span></button>
          <button type="button" className="ikb-btn-0" onClick={() => handleKeyClick("f6")}><span className="ikb-lr ikb-xxxs">F6</span></button>
          <button type="button" className="ikb-btn-0" onClick={() => handleKeyClick("f7")}><span className="ikb-sm"><span className="ikb-left"></span><span className="ikb-left"></span></span><span className="ikb-lr ikb-xxxs">F7</span></button>
          <button type="button" className="ikb-btn-0" onClick={() => handleKeyClick("f8")}><span className="ikb-sm"><span className="ikb-right"></span><span className="ikb-pause"></span></span><span className="ikb-lr ikb-xxxs">F8</span></button>
          <button type="button" className="ikb-btn-0" onClick={() => handleKeyClick("f9")}><span className="ikb-sm"><span className="ikb-right"></span><span className="ikb-right"></span></span><span className="ikb-lr ikb-xxxs">F9</span></button>
          <button type="button" className="ikb-btn-0" onClick={() => handleKeyClick("f10")}><span className="ikb-xs ikb-noxscale"><span className="ikb-emoji">&#128264;</span></span><span className="ikb-lr ikb-xxxs">F10</span></button>
          <button type="button" className="ikb-btn-0" onClick={() => handleKeyClick("f11")}><span className="ikb-xs ikb-noxscale"><span className="ikb-emoji">&#128265;</span></span><span className="ikb-lr ikb-xxxs">F11</span></button>
          <button type="button" className="ikb-btn-0" onClick={() => handleKeyClick("f12")}><span className="ikb-xs ikb-noxscale"><span className="ikb-emoji">&#128266;</span></span><span className="ikb-lr ikb-xxxs">F12</span></button>
          <button type="button" className="ikb-btn-0" onClick={() => handleKeyClick("eject")}><span className="ikb-xs ikb-noxscale">⏏</span></button>
        </div>
        <div className="ikb-row">
          <button type="button" className="ikb-btn-1" onClick={() => handleKeyClick("f13")}><span className="ikb-lr ikb-xxxs">F13</span></button>
          <button type="button" className="ikb-btn-1" onClick={() => handleKeyClick("f14")}><span className="ikb-lr ikb-xxxs">F14</span></button>
          <button type="button" className="ikb-btn-1" onClick={() => handleKeyClick("f15")}><span className="ikb-lr ikb-xxxs">F15</span></button>
        </div>
        <div className="ikb-row">
          <button type="button" className="ikb-btn-1" onClick={() => handleKeyClick("f16")}><span className="ikb-lr ikb-xxxs">F16</span></button>
          <button type="button" className="ikb-btn-1" onClick={() => handleKeyClick("f17")}><span className="ikb-lr ikb-xxxs">F17</span></button>
          <button type="button" className="ikb-btn-1" onClick={() => handleKeyClick("f18")}><span className="ikb-lr ikb-xxxs">F18</span></button>
          <button type="button" className="ikb-btn-1" onClick={() => handleKeyClick("f19")}><span className="ikb-lr ikb-xxxs">F19</span></button>
        </div>
        <div className="ikb-row">
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("`")}><span className="ikb-sm">~<br/>`</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("1")}><span className="ikb-sm">!<br/>1</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("2")}><span className="ikb-sm">@<br/>2</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("3")}><span className="ikb-sm">#<br/>3</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("4")}><span className="ikb-sm">$<br/>4</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("5")}><span className="ikb-sm">%<br/>5</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("6")}><span className="ikb-sm">^<br/>6</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("7")}><span className="ikb-sm">&amp;<br/>7</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("8")}><span className="ikb-sm">*<br/>8</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("9")}><span className="ikb-sm">(<br/>9</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("0")}><span className="ikb-sm">)<br/>0</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("-")}><span className="ikb-sm">_<br/>-</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("=")}><span className="ikb-sm">+<br/>=</span></button>
          <button type="button" className="ikb-btn-3" onClick={() => handleKeyClick("delete")}><span className="ikb-lr ikb-xs">delete</span></button>
        </div>
        <div className="ikb-row">
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("fn")}><span className="ikb-xs">fn</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("home")}><span className="ikb-xs">home</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("page up")}><span className="ikb-xs">page up</span></button>
        </div>
        <div className="ikb-row">
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("clear")}><span className="ikb-xs">clear</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("=")}><span>=</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("/")}><span>/</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("*")}><span>*</span></button>
        </div>
        <div className="ikb-row">
          <button type="button" className="ikb-btn-3" onClick={() => handleKeyClick("tab")}><span className="ikb-ll ikb-xs">tab</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("q")}><span>Q</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("w")}><span>W</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("e")}><span>E</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("r")}><span>R</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("t")}><span>T</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("y")}><span>Y</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("u")}><span>U</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("i")}><span>I</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("o")}><span>O</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("p")}><span>P</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("[")}><span className="ikb-sm">&#123;<br/>[</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("]")}><span className="ikb-sm">&#125;<br/>]</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("\\")}><span className="ikb-sm">|<br/>\</span></button>
        </div>
        <div className="ikb-row">
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("forward delete")}>
            <span className="ikb-xs flex flex-col items-center leading-tight">
              <span>del</span>
              <span className="text-[1.5em] leading-none mt-0.5">⌦</span>
            </span>
          </button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("end")}><span className="ikb-xs">end</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("page down")}><span className="ikb-xs">page down</span></button>
        </div>
        <div className="ikb-row">
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("numpad 7")}><span>7</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("numpad 8")}><span>8</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("numpad 9")}><span>9</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("numpad -")}><span>-</span></button>
        </div>
        <div className="ikb-row">
          <button type="button" className="ikb-btn-4" aria-pressed={capsLock} onClick={() => handleKeyClick("caps lock")}>
            <span className="ikb-ul ikb-xs dot-light" aria-hidden="true">•</span>
            <span className="ikb-ll ikb-xs">caps lock</span>
          </button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("a")}><span>A</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("s")}><span>S</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("d")}><span>D</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("f")}><span>F</span><span className="ikb-bump"></span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("g")}><span>G</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("h")}><span>H</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("j")}><span>J</span><span className="ikb-bump"></span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("k")}><span>K</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("l")}><span>L</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick(";")}><span className="ikb-sm">:<br/>;</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("'")}><span className="ikb-sm">&quot;<br/>'</span></button>
          <button type="button" className="ikb-btn-4" onClick={() => handleKeyClick("return")}><span className="ikb-lr ikb-xs">return</span></button>
        </div>
        <div className="ikb-row"></div>
        <div className="ikb-row">
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("numpad 4")}><span>4</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("numpad 5")}><span>5</span><span className="ikb-bump"></span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("numpad 6")}><span>6</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("numpad +")}><span>+</span></button>
        </div>
        <div className="ikb-row">
          <button type="button" className="ikb-btn-5" onClick={() => handleKeyClick("shift")}><span className="ikb-ll ikb-xs">shift</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("z")}><span>Z</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("x")}><span>X</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("c")}><span>C</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("v")}><span>V</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("b")}><span>B</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("n")}><span>N</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("m")}><span>M</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick(",")}><span className="ikb-sm">&lt;<br/>,</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick(".")}><span className="ikb-sm">&gt;<br/>.</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("/")}><span className="ikb-sm">?<br/>/</span></button>
          <button type="button" className="ikb-btn-5" onClick={() => handleKeyClick("shift")}><span className="ikb-lr ikb-xs">shift</span></button>
        </div>
        <div className="ikb-row">
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("up")}><span><span className="ikb-up"></span></span></button>
        </div>
        <div className="ikb-row">
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("numpad 1")}><span>1</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("numpad 2")}><span>2</span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("numpad 3")}><span>3</span></button>
          <button type="button" className="ikb-btn-10" onClick={() => handleKeyClick("numpad enter")}><span className="ikb-lr ikb-xs">enter</span></button>
        </div>
        <div className="ikb-row">
          <button type="button" className="ikb-btn-7" onClick={() => handleKeyClick("control")}><span className="ikb-ll ikb-xs">control</span></button>
          <button type="button" className="ikb-btn-6" onClick={() => handleKeyClick("option")}><span className="ikb-ul ikb-xxs">alt</span><span className="ikb-ll ikb-xs">option</span></button>
          <button type="button" className="ikb-btn-7" onClick={() => handleKeyClick("command")}>
            <span className="ikb-ll ikb-xs flex items-center gap-[0.3em]">
              <span className="ikb-noxscale text-[1.2em] relative top-[1px]">⌘</span>
              <span>command</span>
            </span>
          </button>
          <button type="button" className="ikb-btn-longest" onClick={() => handleKeyClick("space")}><span></span></button>
          <button type="button" className="ikb-btn-7" onClick={() => handleKeyClick("command")}>
            <span className="ikb-ll ikb-xs flex items-center gap-[0.3em]">
              <span className="ikb-noxscale text-[1.2em] relative top-[1px]">⌘</span>
              <span>command</span>
            </span>
          </button>
          <button type="button" className="ikb-btn-6" onClick={() => handleKeyClick("option")}><span className="ikb-ur ikb-xxs">alt</span><span className="ikb-lr ikb-xs">option</span></button>
          <button type="button" className="ikb-btn-7" onClick={() => handleKeyClick("control")}><span className="ikb-lr ikb-xs">control</span></button>
        </div>
        <div className="ikb-row">
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("left")}><span><span className="ikb-left"></span></span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("down")}><span><span className="ikb-down"></span></span></button>
          <button type="button" className="ikb-btn-2" onClick={() => handleKeyClick("right")}><span><span className="ikb-right"></span></span></button>
        </div>
        <div className="ikb-row">
          <button type="button" className="ikb-btn-9" onClick={() => handleKeyClick("numpad 0")}><span>0</span></button>
          <button type="button" className="ikb-btn-8" onClick={() => handleKeyClick("numpad .")}><span>.</span></button>
        </div>
      </div>
    </div>
  );
}

export default InteractiveKeyboard;

```

---

## `src\components\ui\interactive-particles.tsx`

```tsx
"use client";

import * as React from "react";
import { useEffect, useRef, useState } from "react";
import * as THREE from "three";
import gsap from "gsap";
import { cn } from "@/lib/utils";

/**
 * Interactive Particles
 * Samples an image into thousands of GPU particles that scatter and flow
 * around the cursor via an off-screen "touch texture", with a GSAP intro
 * animation and simplex-noise displacement.
 *
 * Ported from Bruno Imbrizi's Codrops "Interactive Particles" (Three.js)
 * into a single, self-contained, prop-driven React component. The GLSL
 * simplex noise (originally pulled in with glslify) is inlined here.
 */

// Ashima / Stefan Gustavson 2D simplex noise — inlined to replace glslify's
// `glsl-noise/simplex/2d` require from the original shader.
const SIMPLEX_2D = /* glsl */ `
vec3 mod289(vec3 x) { return x - floor(x * (1.0 / 289.0)) * 289.0; }
vec2 mod289(vec2 x) { return x - floor(x * (1.0 / 289.0)) * 289.0; }
vec3 permute(vec3 x) { return mod289(((x * 34.0) + 1.0) * x); }
float snoise(vec2 v) {
  const vec4 C = vec4(0.211324865405187, 0.366025403784439, -0.577350269189626, 0.024390243902439);
  vec2 i  = floor(v + dot(v, C.yy));
  vec2 x0 = v - i + dot(i, C.xx);
  vec2 i1 = (x0.x > x0.y) ? vec2(1.0, 0.0) : vec2(0.0, 1.0);
  vec4 x12 = x0.xyxy + C.xxzz;
  x12.xy -= i1;
  i = mod289(i);
  vec3 p = permute(permute(i.y + vec3(0.0, i1.y, 1.0)) + i.x + vec3(0.0, i1.x, 1.0));
  vec3 m = max(0.5 - vec3(dot(x0, x0), dot(x12.xy, x12.xy), dot(x12.zw, x12.zw)), 0.0);
  m = m * m; m = m * m;
  vec3 x = 2.0 * fract(p * C.www) - 1.0;
  vec3 h = abs(x) - 0.5;
  vec3 ox = floor(x + 0.5);
  vec3 a0 = x - ox;
  m *= 1.79284291400159 - 0.85373472095314 * (a0 * a0 + h * h);
  vec3 g;
  g.x  = a0.x  * x0.x  + h.x  * x0.y;
  g.yz = a0.yz * x12.xz + h.yz * x12.yw;
  return 130.0 * dot(m, g);
}
`;

const VERT = /* glsl */ `
precision highp float;

attribute float pindex;
attribute vec3 position;
attribute vec3 offset;
attribute vec2 uv;
attribute float angle;

uniform mat4 modelViewMatrix;
uniform mat4 projectionMatrix;

uniform float uTime;
uniform float uRandom;
uniform float uDepth;
uniform float uSize;
uniform vec2 uTextureSize;
uniform sampler2D uTexture;
uniform sampler2D uTouch;

varying vec2 vPUv;
varying vec2 vUv;

${SIMPLEX_2D}

float random(float n) {
  return fract(sin(n) * 43758.5453123);
}

void main() {
  vUv = uv;

  vec2 puv = offset.xy / uTextureSize;
  vPUv = puv;

  vec4 colA = texture2D(uTexture, puv);
  float grey = colA.r * 0.21 + colA.g * 0.71 + colA.b * 0.07;

  vec3 displaced = offset;
  displaced.xy += vec2(random(pindex) - 0.5, random(offset.x + pindex) - 0.5) * uRandom;
  float rndz = (random(pindex) + snoise(vec2(pindex * 0.1, uTime * 0.1)));
  displaced.z += rndz * (random(pindex) * 2.0 * uDepth);
  displaced.xy -= uTextureSize * 0.5;

  float t = texture2D(uTouch, puv).r;
  displaced.z += t * 20.0 * rndz;
  displaced.x += cos(angle) * t * 20.0 * rndz;
  displaced.y += sin(angle) * t * 20.0 * rndz;

  float psize = (snoise(vec2(uTime, pindex) * 0.5) + 2.0);
  psize *= max(grey, 0.2);
  psize *= uSize;

  vec4 mvPosition = modelViewMatrix * vec4(displaced, 1.0);
  mvPosition.xyz += position * psize;
  vec4 finalPosition = projectionMatrix * mvPosition;

  gl_Position = finalPosition;
}
`;

const FRAG = /* glsl */ `
precision highp float;

uniform sampler2D uTexture;
uniform vec3 uColor;

varying vec2 vPUv;
varying vec2 vUv;

void main() {
  vec2 uv = vUv;
  vec2 puv = vPUv;

  vec4 colA = texture2D(uTexture, puv);
  float grey = colA.r * 0.21 + colA.g * 0.71 + colA.b * 0.07;

  // Soft, feathered dot instead of a hard-edged circle.
  float radius = 0.5;
  float border = 0.45;
  float dist = radius - distance(uv, vec2(0.5));
  float t = smoothstep(0.0, border, dist);

  // Tint the greyscale value and let dimmer particles fade so tonal images
  // read as an airy field rather than a solid mass.
  vec3 rgb = vec3(grey) * uColor;
  float alpha = t * (0.35 + 0.65 * grey);

  gl_FragColor = vec4(rgb, alpha);
}
`;

/**
 * Off-screen canvas that records the cursor trail and encodes it as a
 * radial-gradient texture the vertex shader reads to push particles.
 */
class TouchTexture {
  size = 64;
  maxAge = 120;
  radius: number;
  trail: { x: number; y: number; age: number; force: number }[] = [];
  canvas: HTMLCanvasElement;
  ctx: CanvasRenderingContext2D;
  texture: THREE.Texture;

  constructor(radius: number) {
    this.radius = radius;
    this.canvas = document.createElement("canvas");
    this.canvas.width = this.canvas.height = this.size;
    this.ctx = this.canvas.getContext("2d")!;
    this.ctx.fillStyle = "black";
    this.ctx.fillRect(0, 0, this.size, this.size);
    this.texture = new THREE.Texture(this.canvas);
  }

  private easeOutSine(t: number, b: number, c: number, d: number) {
    return c * Math.sin((t / d) * (Math.PI / 2)) + b;
  }

  addTouch(x: number, y: number) {
    let force = 0;
    const last = this.trail[this.trail.length - 1];
    if (last) {
      const dx = last.x - x;
      const dy = last.y - y;
      force = Math.min((dx * dx + dy * dy) * 10000, 1);
    }
    this.trail.push({ x, y, age: 0, force });
  }

  update() {
    this.ctx.fillStyle = "black";
    this.ctx.fillRect(0, 0, this.size, this.size);

    for (let i = this.trail.length - 1; i >= 0; i--) {
      this.trail[i].age++;
      if (this.trail[i].age > this.maxAge) this.trail.splice(i, 1);
    }
    for (const point of this.trail) this.drawTouch(point);

    this.texture.needsUpdate = true;
  }

  private drawTouch(point: { x: number; y: number; age: number; force: number }) {
    const pos = { x: point.x * this.size, y: (1 - point.y) * this.size };
    let intensity: number;
    if (point.age < this.maxAge * 0.3) {
      intensity = this.easeOutSine(point.age / (this.maxAge * 0.3), 0, 1, 1);
    } else {
      intensity = this.easeOutSine(1 - (point.age - this.maxAge * 0.3) / (this.maxAge * 0.7), 0, 1, 1);
    }
    intensity *= point.force;

    const radius = this.size * this.radius * intensity;
    const grd = this.ctx.createRadialGradient(pos.x, pos.y, radius * 0.25, pos.x, pos.y, radius);
    grd.addColorStop(0, "rgba(255, 255, 255, 0.2)");
    grd.addColorStop(1, "rgba(0, 0, 0, 0.0)");
    this.ctx.beginPath();
    this.ctx.fillStyle = grd;
    this.ctx.arc(pos.x, pos.y, radius, 0, Math.PI * 2);
    this.ctx.fill();
  }
}

export interface InteractiveParticlesProps {
  /** Initial image URL to sample particles from. Bright pixels become particles. Optional if uploads are allowed. */
  src?: string;
  /** Show an "Upload image" control so the user can supply their own image. Defaults to true. */
  allowUpload?: boolean;
  /** Label for the upload control. Defaults to "Upload image". */
  uploadLabel?: string;
  /** Fired with the uploaded File whenever the user picks an image. */
  onUpload?: (file: File) => void;
  /** Longest edge the source is downscaled to before sampling (caps the particle count). Defaults to 480. */
  maxDimension?: number;
  /** Extra classes for the wrapper element. */
  className?: string;
  /** Wrapper background color. Defaults to black. */
  background?: string;
  /** Particle tint. Defaults to white (keeps the image's greyscale tones). */
  color?: string;
  /** Steady-state particle size multiplier. Defaults to 1.2. */
  size?: number;
  /** Steady-state random spread. Defaults to 1.8. */
  randomness?: number;
  /** Steady-state depth (z displacement). Defaults to 3. */
  depth?: number;
  /** Cursor touch radius (0–1). Defaults to 0.15. */
  touchRadius?: number;
  /** Brightness threshold (0–255) below which pixels are discarded. Defaults to 34. */
  threshold?: number;
}

export function InteractiveParticles({
  src,
  allowUpload = true,
  uploadLabel = "Upload image",
  onUpload,
  maxDimension = 480,
  className,
  background = "#000000",
  color = "#ffffff",
  size = 1.2,
  randomness = 1.8,
  depth = 3.0,
  touchRadius = 0.15,
  threshold = 34,
}: InteractiveParticlesProps) {
  const containerRef = useRef<HTMLDivElement>(null);
  const canvasRef = useRef<HTMLCanvasElement>(null);
  const fileInputRef = useRef<HTMLInputElement>(null);
  const objectUrlRef = useRef<string | null>(null);

  // An uploaded image (as an object URL) takes precedence over `src`.
  const [uploadedSrc, setUploadedSrc] = useState<string | null>(null);
  const effectiveSrc = uploadedSrc ?? src ?? null;

  const handleFile = (file: File | undefined) => {
    if (!file || !file.type.startsWith("image/")) return;
    if (objectUrlRef.current) URL.revokeObjectURL(objectUrlRef.current);
    const url = URL.createObjectURL(file);
    objectUrlRef.current = url;
    setUploadedSrc(url);
    onUpload?.(file);
  };

  // Revoke the last object URL on unmount.
  useEffect(() => {
    return () => {
      if (objectUrlRef.current) URL.revokeObjectURL(objectUrlRef.current);
    };
  }, []);

  useEffect(() => {
    const container = containerRef.current;
    const canvas = canvasRef.current;
    if (!container || !canvas || !effectiveSrc) return;

    let disposed = false;
    const getSize = () => ({
      width: container.clientWidth || 1,
      height: container.clientHeight || 1,
    });
    let view = getSize();

    // ── three basics ─────────────────────────────────────────────────────────
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(50, view.width / view.height, 1, 10000);
    camera.position.z = 300;

    const renderer = new THREE.WebGLRenderer({ canvas, antialias: true, alpha: true });
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    renderer.setSize(view.width, view.height);
    renderer.setClearColor(0x000000, 0);

    let fovHeight = 2 * Math.tan((camera.fov * Math.PI) / 180 / 2) * camera.position.z;

    const clock = new THREE.Clock(true);
    const container3D = new THREE.Object3D();
    scene.add(container3D);

    // ── interaction ─────────────────────────────────────────────────────────
    const raycaster = new THREE.Raycaster();
    const mouseNDC = new THREE.Vector2();
    let rect = canvas.getBoundingClientRect();

    // Objects built once the image loads.
    let object3D: THREE.Mesh | null = null;
    let hitArea: THREE.Mesh | null = null;
    let touch: TouchTexture | null = null;
    let uniforms: Record<string, THREE.IUniform> | null = null;
    let imgWidth = 0;
    let imgHeight = 0;

    const onPointerMove = (e: PointerEvent) => {
      if (!hitArea || !touch) return;
      mouseNDC.x = ((e.clientX - rect.left) / rect.width) * 2 - 1;
      mouseNDC.y = -((e.clientY - rect.top) / rect.height) * 2 + 1;
      raycaster.setFromCamera(mouseNDC, camera);
      const hits = raycaster.intersectObject(hitArea);
      if (hits.length > 0 && hits[0].uv) touch.addTouch(hits[0].uv.x, hits[0].uv.y);
    };
    canvas.addEventListener("pointermove", onPointerMove);

    // ── build particles from the image ───────────────────────────────────────
    const loader = new THREE.TextureLoader();
    loader.setCrossOrigin("anonymous");
    loader.load(effectiveSrc, (texture) => {
      if (disposed) {
        texture.dispose();
        return;
      }
      texture.minFilter = THREE.LinearFilter;
      texture.magFilter = THREE.LinearFilter;

      const image = texture.image as HTMLImageElement;

      // Downscale the sampling grid so uploads of any size stay performant.
      // One kept pixel becomes one particle. The full-res texture is still
      // sampled with normalised UVs, so color detail is preserved.
      const longest = Math.max(image.width, image.height);
      const scaleDown = longest > maxDimension ? maxDimension / longest : 1;
      imgWidth = Math.max(1, Math.round(image.width * scaleDown));
      imgHeight = Math.max(1, Math.round(image.height * scaleDown));
      const numPoints = imgWidth * imgHeight;

      // Read pixels (flipped vertically) to discard dark ones.
      const readCanvas = document.createElement("canvas");
      readCanvas.width = imgWidth;
      readCanvas.height = imgHeight;
      const rctx = readCanvas.getContext("2d")!;
      rctx.scale(1, -1);
      rctx.drawImage(image, 0, 0, imgWidth, imgHeight * -1);
      const colors = Float32Array.from(rctx.getImageData(0, 0, imgWidth, imgHeight).data);

      let numVisible = 0;
      for (let i = 0; i < numPoints; i++) {
        if (colors[i * 4] > threshold) numVisible++;
      }

      uniforms = {
        uTime: { value: 0 },
        uRandom: { value: 1.0 },
        uDepth: { value: 2.0 },
        uSize: { value: 0.0 },
        uTextureSize: { value: new THREE.Vector2(imgWidth, imgHeight) },
        uTexture: { value: texture },
        uTouch: { value: null },
        uColor: { value: new THREE.Color(color) },
      };

      const material = new THREE.RawShaderMaterial({
        uniforms,
        vertexShader: VERT,
        fragmentShader: FRAG,
        depthTest: false,
        transparent: true,
      });

      const geometry = new THREE.InstancedBufferGeometry();

      const positions = new THREE.BufferAttribute(new Float32Array(4 * 3), 3);
      positions.setXYZ(0, -0.5, 0.5, 0.0);
      positions.setXYZ(1, 0.5, 0.5, 0.0);
      positions.setXYZ(2, -0.5, -0.5, 0.0);
      positions.setXYZ(3, 0.5, -0.5, 0.0);
      geometry.setAttribute("position", positions);

      const uvs = new THREE.BufferAttribute(new Float32Array(4 * 2), 2);
      uvs.setXY(0, 0.0, 0.0);
      uvs.setXY(1, 1.0, 0.0);
      uvs.setXY(2, 0.0, 1.0);
      uvs.setXY(3, 1.0, 1.0);
      geometry.setAttribute("uv", uvs);

      geometry.setIndex(new THREE.BufferAttribute(new Uint16Array([0, 2, 1, 2, 3, 1]), 1));

      const indices = new Uint16Array(numVisible);
      const offsets = new Float32Array(numVisible * 3);
      const angles = new Float32Array(numVisible);
      for (let i = 0, j = 0; i < numPoints; i++) {
        if (colors[i * 4] <= threshold) continue;
        offsets[j * 3 + 0] = i % imgWidth;
        offsets[j * 3 + 1] = Math.floor(i / imgWidth);
        indices[j] = i;
        angles[j] = Math.random() * Math.PI;
        j++;
      }
      geometry.setAttribute("pindex", new THREE.InstancedBufferAttribute(indices, 1, false));
      geometry.setAttribute("offset", new THREE.InstancedBufferAttribute(offsets, 3, false));
      geometry.setAttribute("angle", new THREE.InstancedBufferAttribute(angles, 1, false));

      object3D = new THREE.Mesh(geometry, material);
      container3D.add(object3D);

      // Invisible hit plane for cursor → uv raycasting.
      const hitGeo = new THREE.PlaneGeometry(imgWidth, imgHeight, 1, 1);
      const hitMat = new THREE.MeshBasicMaterial({ color: 0xffffff, wireframe: true, depthTest: false });
      hitMat.visible = false;
      hitArea = new THREE.Mesh(hitGeo, hitMat);
      container3D.add(hitArea);

      touch = new TouchTexture(touchRadius);
      uniforms.uTouch.value = touch.texture;

      applyScale();

      // Intro animation.
      gsap.fromTo(uniforms.uSize, { value: 0.5 }, { value: size, duration: 1.0 });
      gsap.to(uniforms.uRandom, { value: randomness, duration: 1.0 });
      gsap.fromTo(uniforms.uDepth, { value: 40.0 }, { value: depth, duration: 1.5 });
    });

    const applyScale = () => {
      if (!object3D || !hitArea || !imgHeight) return;
      const scale = fovHeight / imgHeight;
      object3D.scale.set(scale, scale, 1);
      hitArea.scale.set(scale, scale, 1);
    };

    // ── resize ────────────────────────────────────────────────────────────────
    const applySize = () => {
      view = getSize();
      camera.aspect = view.width / view.height;
      camera.updateProjectionMatrix();
      fovHeight = 2 * Math.tan((camera.fov * Math.PI) / 180 / 2) * camera.position.z;
      renderer.setSize(view.width, view.height);
      rect = canvas.getBoundingClientRect();
      applyScale();
    };
    const resizeObserver = new ResizeObserver(applySize);
    resizeObserver.observe(container);
    window.addEventListener("resize", applySize);

    // ── loop ──────────────────────────────────────────────────────────────────
    renderer.setAnimationLoop(() => {
      const delta = clock.getDelta();
      if (touch) touch.update();
      if (uniforms) uniforms.uTime.value += delta;
      renderer.render(scene, camera);
    });

    // ── cleanup ─────────────────────────────────────────────────────────────
    return () => {
      disposed = true;
      renderer.setAnimationLoop(null);
      canvas.removeEventListener("pointermove", onPointerMove);
      window.removeEventListener("resize", applySize);
      resizeObserver.disconnect();
      if (uniforms) gsap.killTweensOf([uniforms.uSize, uniforms.uRandom, uniforms.uDepth]);
      if (object3D) {
        object3D.geometry.dispose();
        (object3D.material as THREE.Material).dispose();
      }
      if (hitArea) {
        hitArea.geometry.dispose();
        (hitArea.material as THREE.Material).dispose();
      }
      if (touch) touch.texture.dispose();
      // Dispose GL resources but keep the context — the same canvas reuses its
      // single WebGL context when the effect re-runs (e.g. after an upload).
      renderer.dispose();
    };
  }, [effectiveSrc, color, size, randomness, depth, touchRadius, threshold, maxDimension]);

  return (
    <div
      ref={containerRef}
      className={cn("relative h-full w-full overflow-hidden", className)}
      style={{ background }}
    >
      <canvas ref={canvasRef} className="block h-full w-full" />

      {allowUpload && (
        <>
          <input
            ref={fileInputRef}
            type="file"
            accept="image/*"
            className="hidden"
            onChange={(e) => {
              handleFile(e.target.files?.[0]);
              e.target.value = "";
            }}
          />
          <button
            type="button"
            onClick={() => fileInputRef.current?.click()}
            className="group absolute right-4 top-4 z-10 inline-flex items-center gap-1.5 rounded-full border border-white/10 bg-white/5 px-3 py-1.5 text-[11px] font-medium text-white/60 backdrop-blur-sm transition-all duration-200 hover:border-white/20 hover:bg-white/10 hover:text-white/90"
          >
            <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" aria-hidden="true">
              <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4" />
              <polyline points="17 8 12 3 7 8" />
              <line x1="12" y1="3" x2="12" y2="15" />
            </svg>
            {uploadLabel}
          </button>
        </>
      )}
    </div>
  );
}

export default InteractiveParticles;

```

---

## `src\components\ui\item.tsx`

```tsx
import * as React from "react"
import { Slot } from "@radix-ui/react-slot"
import { cva, type VariantProps } from "class-variance-authority"

import { cn } from "@/lib/utils"
import { Separator } from "@/components/ui/separator"

function ItemGroup({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      role="list"
      data-slot="item-group"
      className={cn("group/item-group flex flex-col", className)}
      {...props}
    />
  )
}

function ItemSeparator({
  className,
  ...props
}: React.ComponentProps<typeof Separator>) {
  return (
    <Separator
      data-slot="item-separator"
      orientation="horizontal"
      className={cn("my-0", className)}
      {...props}
    />
  )
}

const itemVariants = cva(
  "group/item flex items-center border border-transparent text-sm rounded-md transition-colors [a]:hover:bg-accent/50 [a]:transition-colors duration-100 flex-wrap outline-none focus-visible:border-ring focus-visible:ring-ring/50 focus-visible:ring-[3px]",
  {
    variants: {
      variant: {
        default: "bg-transparent",
        outline: "border-border",
        muted: "bg-muted/50",
      },
      size: {
        default: "p-4 gap-4 ",
        sm: "py-3 px-4 gap-2.5",
      },
    },
    defaultVariants: {
      variant: "default",
      size: "default",
    },
  }
)

function Item({
  className,
  variant = "default",
  size = "default",
  asChild = false,
  ...props
}: React.ComponentProps<"div"> &
  VariantProps<typeof itemVariants> & { asChild?: boolean }) {
  const Comp = asChild ? Slot : "div"
  return (
    <Comp
      data-slot="item"
      data-variant={variant}
      data-size={size}
      className={cn(itemVariants({ variant, size, className }))}
      {...props}
    />
  )
}

const itemMediaVariants = cva(
  "flex shrink-0 items-center justify-center gap-2 group-has-[[data-slot=item-description]]/item:self-start [&_svg]:pointer-events-none group-has-[[data-slot=item-description]]/item:translate-y-0.5",
  {
    variants: {
      variant: {
        default: "bg-transparent",
        icon: "size-8 border rounded-sm bg-muted [&_svg:not([class*='size-'])]:size-4",
        image:
          "size-10 rounded-sm overflow-hidden [&_img]:size-full [&_img]:object-cover",
      },
    },
    defaultVariants: {
      variant: "default",
    },
  }
)

function ItemMedia({
  className,
  variant = "default",
  ...props
}: React.ComponentProps<"div"> & VariantProps<typeof itemMediaVariants>) {
  return (
    <div
      data-slot="item-media"
      data-variant={variant}
      className={cn(itemMediaVariants({ variant, className }))}
      {...props}
    />
  )
}

function ItemContent({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="item-content"
      className={cn(
        "flex flex-1 flex-col gap-1 [&+[data-slot=item-content]]:flex-none",
        className
      )}
      {...props}
    />
  )
}

function ItemTitle({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="item-title"
      className={cn(
        "flex w-fit items-center gap-2 text-sm leading-snug font-medium",
        className
      )}
      {...props}
    />
  )
}

function ItemDescription({ className, ...props }: React.ComponentProps<"p">) {
  return (
    <p
      data-slot="item-description"
      className={cn(
        "text-muted-foreground line-clamp-2 text-sm leading-normal font-normal text-balance",
        "[&>a:hover]:text-primary [&>a]:underline [&>a]:underline-offset-4",
        className
      )}
      {...props}
    />
  )
}

function ItemActions({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="item-actions"
      className={cn("flex items-center gap-2", className)}
      {...props}
    />
  )
}

function ItemHeader({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="item-header"
      className={cn(
        "flex basis-full items-center justify-between gap-2",
        className
      )}
      {...props}
    />
  )
}

function ItemFooter({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="item-footer"
      className={cn(
        "flex basis-full items-center justify-between gap-2",
        className
      )}
      {...props}
    />
  )
}

export {
  Item,
  ItemMedia,
  ItemContent,
  ItemActions,
  ItemGroup,
  ItemSeparator,
  ItemTitle,
  ItemDescription,
  ItemHeader,
  ItemFooter,
}

```

---

## `src\components\ui\kbd.tsx`

```tsx
import { cn } from "@/lib/utils"

function Kbd({ className, ...props }: React.ComponentProps<"kbd">) {
  return (
    <kbd
      data-slot="kbd"
      className={cn(
        "bg-muted text-muted-foreground pointer-events-none inline-flex h-5 w-fit min-w-5 items-center justify-center gap-1 rounded-sm px-1 font-sans text-xs font-medium select-none",
        "[&_svg:not([class*='size-'])]:size-3",
        "[[data-slot=tooltip-content]_&]:bg-background/20 [[data-slot=tooltip-content]_&]:text-background dark:[[data-slot=tooltip-content]_&]:bg-background/10",
        className
      )}
      {...props}
    />
  )
}

function KbdGroup({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <kbd
      data-slot="kbd-group"
      className={cn("inline-flex items-center gap-1", className)}
      {...props}
    />
  )
}

export { Kbd, KbdGroup }

```

---

## `src\components\ui\kinetic-text-loader.tsx`

```tsx
"use client";

import React from "react";
import { cn } from "@/lib/utils";

export interface KineticTextLoaderProps extends React.HTMLAttributes<HTMLDivElement> {
  text?: string;
}

export function KineticTextLoader({ 
  className, 
  text = "Loading", 
  ...props 
}: KineticTextLoaderProps) {
  const letters = text.split("");

  return (
    <div 
      className={cn("relative flex items-center justify-center font-light", className)} 
      style={{ fontFamily: "'Roboto', sans-serif" }}
      {...props}
    >
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Roboto:wght@300&display=swap');
        
        @keyframes ktl-dotMove {
          0%, 100% { transform: rotate(180deg) translate(-80px, -10px) rotate(-180deg); }
          50% { transform: rotate(0deg) translate(-81px, 10px) rotate(0deg); }
        }
        @keyframes ktl-letterStretch {
          0%, 100% { transform: scale(1, 0.35); transform-origin: 100% 75%; }
          8%, 28% { transform: scale(1, 1.4); transform-origin: 100% 67%; }
          37% { transform: scale(1, 0.875); transform-origin: 100% 75%; }
          46% { transform: scale(1, 1.03); transform-origin: 100% 75%; }
          50%, 97% { transform: scale(1); transform-origin: 100% 75%; }
        }
        @keyframes ktl-l-bounce {
          0%, 45%, 70%, 100% { transform: scaleY(1.11); }
          49% { transform: scaleY(0.31); }
          50% { transform: scaleY(0.16); }
          53% { transform: scaleY(0.63); }
          60% { transform: scaleY(1.275); }
          68% { transform: scaleY(1.04); }
        }
      `}</style>
      
      <div className="relative scale-75 md:scale-90 lg:scale-100">
        {/* The moving dot */}
        <div 
          className="absolute z-10 top-[40px] left-[85px] w-[6px] h-[6px] bg-neutral-800 dark:bg-neutral-200 rounded-full"
          style={{ animation: "ktl-dotMove 1800ms cubic-bezier(0.25,0.25,0.75,0.75) infinite" }}
        />
        
        <p className="relative m-0 whitespace-nowrap text-[3.75rem] text-neutral-800 dark:text-neutral-200" aria-label={text}>
          {letters.map((char, index) => {
            if (index === 0 && char.toUpperCase() === 'L') {
              return (
                <span 
                  key={index} 
                  className="inline-block relative tracking-[8px] transform origin-[100%_70%]"
                  style={{ animation: "ktl-l-bounce 1800ms cubic-bezier(0.25,0.25,0.75,0.75) infinite" }}
                >
                  {char}
                </span>
              );
            }
            
            if (index === 4 && char.toLowerCase() === 'i') {
              return (
                <span 
                  key={index} 
                  className="inline-block relative tracking-[8px] transform origin-[100%_70%]"
                  style={{ animation: "ktl-letterStretch 1800ms cubic-bezier(0.25,0.23,0.73,0.75) infinite" }}
                >
                  {char === 'i' ? 'ı' : char}
                </span>
              );
            }

            return (
              <span key={index} className="inline-block relative tracking-[8px]">
                {char}
              </span>
            );
          })}
        </p>
      </div>
    </div>
  );
}

export default KineticTextLoader;

```

---

## `src\components\ui\label.tsx`

```tsx
'use client'

import * as React from 'react'
import * as LabelPrimitive from '@radix-ui/react-label'

import { cn } from '@/lib/utils'

function Label({ className, ...props }: React.ComponentProps<typeof LabelPrimitive.Root>) {
    return (
        <LabelPrimitive.Root
            data-slot="label"
            className={cn('select-none text-sm font-medium leading-none peer-disabled:cursor-not-allowed peer-disabled:opacity-50 group-data-[disabled=true]:pointer-events-none group-data-[disabled=true]:opacity-50', className)}
            {...props}
        />
    )
}

export { Label }

```

---

## `src\components\ui\light-lines.tsx`

```tsx
"use client";

import { useEffect, useRef } from "react";
import { cn } from "@/lib/utils";

interface LightLinesProps {
    /** Additional CSS classes */
    className?: string;
    /** Lines opacity (0-1) */
    linesOpacity?: number;
    /** Lights opacity (0-1) */
    lightsOpacity?: number;
    /** Animation speed multiplier */
    speedMultiplier?: number;
    /** Primary gradient color (from) */
    gradientFrom?: string;
    /** Primary gradient color (to) */
    gradientTo?: string;
    /** Light color */
    lightColor?: string;
    /** Line color */
    lineColor?: string;
    /** Children content to overlay */
    children?: React.ReactNode;
}

interface AnimatedLightRef {
    element: SVGPathElement | null;
    from: number;
    to: number;
    duration: number;
}

export function LightLines({
    className,
    linesOpacity = 0.05,
    lightsOpacity = 0.9,
    speedMultiplier = 1,
    gradientFrom = "#2462F6",
    gradientTo = "#5999F8",
    lightColor = "#fff",
    lineColor = "#fff",
    children,
}: LightLinesProps) {
    const containerRef = useRef<HTMLDivElement>(null);
    const animationsRef = useRef<AnimatedLightRef[]>([]);
    const frameRef = useRef<number | null>(null);

    useEffect(() => {
        // Light elements configuration
        const lightsDown = [
            { selector: ".light4", from: -1080, to: 1080 },
            { selector: ".light5", from: -1080, to: 1080 },
            { selector: ".light6", from: -1080, to: 1080 },
            { selector: ".light7", from: -1080, to: 1080 },
            { selector: ".light8", from: -1080, to: 1080 },
            { selector: ".light11", from: -1080, to: 1080 },
            { selector: ".light12", from: -1080, to: 1080 },
            { selector: ".light13", from: -1080, to: 1080 },
            { selector: ".light14", from: -1080, to: 1080 },
            { selector: ".light15", from: -1080, to: 1080 },
            { selector: ".light16", from: -1080, to: 1080 },
        ];

        const lightsUp = [
            { selector: ".light1", from: 1080, to: -1080 },
            { selector: ".light2", from: 1080, to: -1080 },
            { selector: ".light3", from: 1080, to: -1080 },
            { selector: ".light9", from: 1080, to: -1080 },
            { selector: ".light10", from: 1080, to: -1080 },
            { selector: ".light17", from: 1080, to: -1080 },
        ];

        const container = containerRef.current;
        if (!container) return;

        // Initialize animations
        const allLights = [...lightsDown, ...lightsUp];
        animationsRef.current = allLights.map((light) => {
            const element = container.querySelector(light.selector) as SVGPathElement | null;
            const duration = (Math.floor(Math.random() * 59) + 2) * 0.5 + 0.5;
            return {
                element,
                from: light.from,
                to: light.to,
                duration: duration / speedMultiplier,
            };
        });

        // Animation state for each light
        const animationState = animationsRef.current.map(() => ({
            startTime: performance.now() - Math.random() * 5000, // Stagger start times
            currentY: 0,
        }));

        let isVisible = false;

        const animate = (time: number) => {
            if (!isVisible) {
                frameRef.current = requestAnimationFrame(animate);
                return;
            }

            animationsRef.current.forEach((ref, index) => {
                if (!ref.element) return;

                const state = animationState[index];
                const elapsed = (time - state.startTime) / 1000; // Convert to seconds
                const progress = (elapsed % ref.duration) / ref.duration;
                const currentY = ref.from + (ref.to - ref.from) * progress;

                ref.element.style.transform = `translateY(${currentY}px)`;
            });

            frameRef.current = requestAnimationFrame(animate);
        };

        const observer = new IntersectionObserver(
            ([entry]) => {
                isVisible = entry.isIntersecting;
            },
            { threshold: 0 }
        );

        observer.observe(container);

        frameRef.current = requestAnimationFrame(animate);

        return () => {
            if (frameRef.current) {
                cancelAnimationFrame(frameRef.current);
            }
            observer.disconnect();
        };
    }, [speedMultiplier]);

    return (
        <div
            ref={containerRef}
            className={cn(
                "absolute inset-0 h-full w-full flex justify-center overflow-hidden",
                className
            )}
            style={{
                background: `linear-gradient(180deg, ${gradientFrom} 0%, ${gradientTo} 100%)`,
            }}
        >
            <svg
                className="absolute h-full"
                xmlns="http://www.w3.org/2000/svg"
                x="0px"
                y="0px"
                viewBox="0 0 1920 1080"
                preserveAspectRatio="none"
            >
                {/* Static Lines */}
                <g className="lines" style={{ opacity: linesOpacity }}>
                    <rect
                        className="line"
                        x="1253.6"
                        width="4.5"
                        height="1080"
                        style={{ fill: lineColor, fillRule: "evenodd", clipRule: "evenodd" }}
                    />
                    <rect
                        className="line"
                        x="873.3"
                        width="1.8"
                        height="1080"
                        style={{ fill: lineColor, fillRule: "evenodd", clipRule: "evenodd" }}
                    />
                    <rect
                        className="line"
                        x="1100"
                        width="1.8"
                        height="1080"
                        style={{ fill: lineColor, fillRule: "evenodd", clipRule: "evenodd" }}
                    />
                    <rect
                        className="line"
                        x="1547.1"
                        width="4.5"
                        height="1080"
                        style={{ fill: lineColor, fillRule: "evenodd", clipRule: "evenodd" }}
                    />
                    <rect
                        className="line"
                        x="615"
                        width="4.5"
                        height="1080"
                        style={{ fill: lineColor, fillRule: "evenodd", clipRule: "evenodd" }}
                    />
                    <rect
                        className="line"
                        x="684.6"
                        width="1.8"
                        height="1080"
                        style={{ fill: lineColor, fillRule: "evenodd", clipRule: "evenodd" }}
                    />
                    <rect
                        className="line"
                        x="1369.4"
                        width="1.8"
                        height="1080"
                        style={{ fill: lineColor, fillRule: "evenodd", clipRule: "evenodd" }}
                    />
                    <rect
                        className="line"
                        x="1310.2"
                        width="0.9"
                        height="1080"
                        style={{ fill: lineColor, fillRule: "evenodd", clipRule: "evenodd" }}
                    />
                    <rect
                        className="line"
                        x="1233.4"
                        width="0.9"
                        height="1080"
                        style={{ fill: lineColor, fillRule: "evenodd", clipRule: "evenodd" }}
                    />
                    <rect
                        className="line"
                        x="124.2"
                        width="0.9"
                        height="1080"
                        style={{ fill: lineColor, fillRule: "evenodd", clipRule: "evenodd" }}
                    />
                    <rect
                        className="line"
                        x="1818.4"
                        width="4.5"
                        height="1080"
                        style={{ fill: lineColor, fillRule: "evenodd", clipRule: "evenodd" }}
                    />
                    <rect
                        className="line"
                        x="70.3"
                        width="4.5"
                        height="1080"
                        style={{ fill: lineColor, fillRule: "evenodd", clipRule: "evenodd" }}
                    />
                    <rect
                        className="line"
                        x="1618.6"
                        width="1.8"
                        height="1080"
                        style={{ fill: lineColor, fillRule: "evenodd", clipRule: "evenodd" }}
                    />
                    <rect
                        className="line"
                        x="455.9"
                        width="1.8"
                        height="1080"
                        style={{ fill: lineColor, fillRule: "evenodd", clipRule: "evenodd" }}
                    />
                    <rect
                        className="line"
                        x="328.7"
                        width="1.8"
                        height="1080"
                        style={{ fill: lineColor, fillRule: "evenodd", clipRule: "evenodd" }}
                    />
                    <rect
                        className="line"
                        x="300.8"
                        width="4.6"
                        height="1080"
                        style={{ fill: lineColor, fillRule: "evenodd", clipRule: "evenodd" }}
                    />
                    <rect
                        className="line"
                        x="1666.4"
                        width="0.9"
                        height="1080"
                        style={{ fill: lineColor, fillRule: "evenodd", clipRule: "evenodd" }}
                    />
                </g>

                {/* Animated Lights */}
                <g className="lights" style={{ opacity: lightsOpacity }}>
                    <path
                        className="light1 light"
                        style={{ fill: lightColor, fillRule: "evenodd", clipRule: "evenodd" }}
                        d="M619.5,298.4H615v19.5h4.5V298.4z M619.5,674.8H615v9.8h4.5V674.8z M619.5,135.1H615v5.6h4.5V135.1z M619.5,55.5H615v68.7h4.5V55.5z"
                    />
                    <path
                        className="light2 light"
                        style={{ fill: lightColor, fillRule: "evenodd", clipRule: "evenodd" }}
                        d="M1258.2,531.9h-4.5v8.1h4.5V531.9z M1258.2,497.9h-4.5v17.9h4.5V497.9z M1258.2,0h-4.5v25.3h4.5V0z M1258.2,252.2h-4.5v42.4h4.5V252.2z"
                    />
                    <path
                        className="light3 light"
                        style={{ fill: lightColor, fillRule: "evenodd", clipRule: "evenodd" }}
                        d="M875.1,123.8h-1.8v4h1.8V123.8z M875.1,289.4h-1.8v24.1h1.8V289.4z M875.1,0h-1.8v31.4h1.8V0z M875.1,50.2h-1.8v11.5h1.8V50.2z"
                    />
                    <path
                        className="light4 light"
                        style={{ fill: lightColor, fillRule: "evenodd", clipRule: "evenodd" }}
                        d="M1101.8,983.8h-1.8v8.2h1.8V983.8z M1101.8,1075.9h-1.8v4.1h1.8V1075.9z M1101.8,873.7h-1.8v4.2h1.8V873.7z M1101.8,851h-1.8v18.2h1.8V851z"
                    />
                    <path
                        className="light5 light"
                        style={{ fill: lightColor, fillRule: "evenodd", clipRule: "evenodd" }}
                        d="M686.4,822.7h-1.8v3.8h1.8V822.7z M686.4,928.4h-1.8v23h1.8V928.4z M686.4,1043.8h-1.8v36.2h1.8V1043.8z"
                    />
                    <path
                        className="light6 light"
                        style={{ fill: lightColor, fillRule: "evenodd", clipRule: "evenodd" }}
                        d="M1551.6,860.9h-4.4v-34.1h4.4V860.9z M1551.6,533.5h-4.4v-13.9h4.4V533.5z M1551.6,1080h-4.4v-89.1h4.4V1080z"
                    />
                    <path
                        className="light7 light"
                        style={{ fill: lightColor, fillRule: "evenodd", clipRule: "evenodd" }}
                        d="M1311.1,707.7h-0.9V698h0.9V707.7z M1311.1,436.8h-0.9v-58.9h0.9V436.8z M1311.1,140.7h-0.9V48h0.9V140.7z"
                    />
                    <path
                        className="light8 light"
                        style={{ fill: lightColor, fillRule: "evenodd", clipRule: "evenodd" }}
                        d="M125.1,514.5h-0.9v-9.7h0.9V514.5z M125.1,243.6h-0.9v-58.9h0.9V243.6z"
                    />
                    <path
                        className="light9 light"
                        style={{ fill: lightColor, fillRule: "evenodd", clipRule: "evenodd" }}
                        d="M305.4,806.7h-4.6v-42.5h4.6V806.7z M305.4,398.5h-4.6v-17.3h4.6V398.5z M305.4,1080h-4.6V968.8h4.6V1080z"
                    />
                    <path
                        className="light10 light"
                        style={{ fill: lightColor, fillRule: "evenodd", clipRule: "evenodd" }}
                        d="M1822.9,170.7h-4.5v13.7h4.5V170.7z M1822.9,435.1h-4.5v6.8h4.5V435.1z M1822.9,55.9h-4.5v4h4.5V55.9z M1822.9,0h-4.5v48.3h4.5V0z"
                    />
                    <path
                        className="light11 light"
                        style={{ fill: lightColor, fillRule: "evenodd", clipRule: "evenodd" }}
                        d="M1666.4,331.5h0.9v9.7h-0.9V331.5z M1666.4,602.4h0.9v58.9h-0.9V602.4z M1666.4,898.5h0.9v92.7h-0.9V898.5z"
                    />
                    <path
                        className="light12 light"
                        style={{ fill: lightColor, fillRule: "evenodd", clipRule: "evenodd" }}
                        d="M1620.4,200.7h-1.8v6.4h1.8V200.7z M1620.4,469.1h-1.8v39h1.8V469.1z M1620.4,0h-1.8v51h1.8V0z M1620.4,81.3h-1.8V100h1.8V81.3z"
                    />
                    <path
                        className="light13 light"
                        style={{ fill: lightColor, fillRule: "evenodd", clipRule: "evenodd" }}
                        d="M74.8,201h-4.5v16.2h4.5V201z M74.8,512.3h-4.5v8.1h4.5V512.3z M74.8,65.8h-4.5v4.6h4.5V65.8z M74.8,0h-4.5v56.8h4.5V0z"
                    />
                    <path
                        className="light14 light"
                        style={{ fill: lightColor, fillRule: "evenodd", clipRule: "evenodd" }}
                        d="M1371.2,655.3h-1.8v6.3h1.8V655.3z M1371.2,829.7h-1.8v37.9h1.8V829.7z M1371.2,1020.3h-1.8v59.7h1.8V1020.3z"
                    />
                    <path
                        className="light15 light"
                        style={{ fill: lightColor, fillRule: "evenodd", clipRule: "evenodd" }}
                        d="M1234.3,898.1h-0.9v-4.9h0.9V898.1z M1234.3,762.5h-0.9v-29.5h0.9V762.5z M1234.3,614.4h-0.9v-46.4h0.9V614.4z"
                    />
                    <path
                        className="light16 light"
                        style={{ fill: lightColor, fillRule: "evenodd", clipRule: "evenodd" }}
                        d="M457.7,1010.8h-1.8v-18.1h1.8V1010.8z M457.7,507.5h-1.8V398h1.8V507.5z"
                    />
                    <path
                        className="light17 light"
                        style={{ fill: lightColor, fillRule: "evenodd", clipRule: "evenodd" }}
                        d="M330.5,170.7h-1.8v13.7h1.8V170.7z M330.5,435.1h-1.8v6.8h1.8V435.1z M330.5,55.9h-1.8v4h1.8V55.9z M330.5,0h-1.8v48.3h1.8V0z"
                    />
                </g>
            </svg>

            {/* Content overlay */}
            {children && (
                <div className="relative z-10 w-full h-full">{children}</div>
            )}
        </div>
    );
}

export default LightLines;

```

---

## `src\components\ui\line-hover-link.tsx`

```tsx
"use client";

import * as React from "react";
import { cn } from "@/lib/utils";

const lineHoverStyles = `
.link-hover {
  cursor: pointer;
  position: relative;
  display: inline-flex;
  width: fit-content;
  white-space: nowrap;
  color: currentColor;
  text-decoration: none;
  outline: none;
}

.link-hover::before,
.link-hover::after {
  position: absolute;
  left: 0;
  top: 100%;
  width: 100%;
  height: 1px;
  background: currentColor;
  pointer-events: none;
}

.link-hover::before {
  content: "";
}

.link-hover:focus-visible {
  border-radius: 0.25rem;
  outline: 2px solid color-mix(in srgb, currentColor 45%, transparent);
  outline-offset: 0.25rem;
}

.link-hover--slide::before {
  transform: scale3d(0, 1, 1);
  transform-origin: 100% 50%;
  transition: transform 0.3s ease;
}

.link-hover--slide:hover::before,
.link-hover--slide:focus-visible::before {
  transform: scale3d(1, 1, 1);
  transform-origin: 0% 50%;
}

.link-hover--double::before {
  transform: scale3d(0, 1, 1);
  transform-origin: 100% 50%;
  transition: transform 0.3s cubic-bezier(0.7, 0, 0.2, 1);
}

.link-hover--double:hover::before,
.link-hover--double:focus-visible::before {
  transform: scale3d(1, 1, 1);
  transform-origin: 0% 50%;
  transition-timing-function: cubic-bezier(0.4, 1, 0.8, 1);
}

.link-hover--double::after {
  content: "";
  top: calc(100% + 4px);
  transform: scale3d(0, 1, 1);
  transform-origin: 0% 50%;
  transition: transform 0.3s cubic-bezier(0.7, 0, 0.2, 1);
}

.link-hover--double:hover::after,
.link-hover--double:focus-visible::after {
  transform: scale3d(1, 1, 1);
  transform-origin: 100% 50%;
  transition-timing-function: cubic-bezier(0.4, 1, 0.8, 1);
}

.link-hover--grow::before {
  transform: scale3d(0, 1, 1);
  transform-origin: 100% 50%;
  transition: transform 0.3s cubic-bezier(0.2, 1, 0.8, 1);
}

.link-hover--grow:hover::before,
.link-hover--grow:focus-visible::before {
  transform: scale3d(1, 2, 1);
  transform-origin: 0% 50%;
  transition-timing-function: cubic-bezier(0.7, 0, 0.2, 1);
}

.link-hover--grow::after {
  content: "";
  top: calc(100% + 4px);
  transform: scale3d(0, 1, 1);
  transform-origin: 100% 50%;
  transition: transform 0.4s 0.1s cubic-bezier(0.2, 1, 0.8, 1);
}

.link-hover--grow:hover::after,
.link-hover--grow:focus-visible::after {
  transform: scale3d(1, 1, 1);
  transform-origin: 0% 50%;
}

.link-hover--strike {
  padding-inline: 0.625rem;
}

.link-hover--strike::before {
  top: 50%;
  height: 2px;
  transform: scale3d(0, 1, 1);
  transform-origin: 100% 50%;
  transition: transform 0.3s cubic-bezier(0.4, 1, 0.8, 1);
}

.link-hover--strike:hover::before,
.link-hover--strike:focus-visible::before {
  transform: scale3d(1, 1, 1);
  transform-origin: 0% 50%;
}

.link-hover--strike span {
  display: inline-block;
  transition: transform 0.3s cubic-bezier(0.4, 1, 0.8, 1);
}

.link-hover--strike:hover span,
.link-hover--strike:focus-visible span {
  transform: scale3d(1.08, 1.08, 1);
}

.link-hover--fade::before,
.link-hover--fade::after {
  opacity: 0;
  transform: translate3d(0, 3px, 0);
  transform-origin: 50% 0%;
  transition: transform 0.3s cubic-bezier(0.2, 1, 0.8, 1), opacity 0.3s cubic-bezier(0.2, 1, 0.8, 1);
}

.link-hover--fade::after {
  content: "";
  left: 15%;
  top: calc(100% + 4px);
  width: 70%;
}

.link-hover--fade:hover::before,
.link-hover--fade:hover::after,
.link-hover--fade:focus-visible::before,
.link-hover--fade:focus-visible::after {
  opacity: 1;
  transform: translate3d(0, 0, 0);
}

.link-hover--fade::before,
.link-hover--fade:hover::after,
.link-hover--fade:focus-visible::after {
  transition-delay: 0.1s;
}

.link-hover--pulse::before {
  top: 100%;
  height: 10px;
  opacity: 0;
}

.link-hover--pulse:hover::before,
.link-hover--pulse:focus-visible::before {
  opacity: 1;
  animation: line-hover-line-up 0.3s ease forwards;
}

.link-hover--pulse::after {
  content: "";
  opacity: 0;
  transition: opacity 0.3s;
}

.link-hover--pulse:hover::after,
.link-hover--pulse:focus-visible::after {
  opacity: 1;
  transition-delay: 0.3s;
}

.link-hover--swap::before {
  transform: scale3d(0, 1, 1);
  transform-origin: 0% 50%;
  transition: transform 0.3s ease;
}

.link-hover--swap:hover::before,
.link-hover--swap:focus-visible::before {
  transform: scale3d(1, 1, 1);
}

.link-hover--swap::after {
  content: "";
  top: calc(100% + 4px);
  transform-origin: 100% 50%;
  transition: transform 0.3s ease;
}

.link-hover--swap:hover::after,
.link-hover--swap:focus-visible::after {
  transform: scale3d(0, 1, 1);
}

.link-hover--sweep {
  padding-inline: 0.25rem;
}

.link-hover--sweep::before {
  top: 0;
  height: 100%;
  background: color-mix(in srgb, currentColor 12%, transparent);
  opacity: 0;
}

.link-hover--sweep:hover::before,
.link-hover--sweep:focus-visible::before {
  opacity: 1;
  animation: line-hover-cover-up 0.3s ease forwards;
}

.link-hover--sweep::after {
  content: "";
  transition: opacity 0.3s ease;
}

.link-hover--sweep:hover::after,
.link-hover--sweep:focus-visible::after {
  opacity: 0;
}

.link-hover--bounce::before {
  height: 7px;
  border-radius: 999px;
  transform: scale3d(1, 1, 1);
  transition: transform 0.2s, opacity 0.2s;
  transition-timing-function: cubic-bezier(0.2, 0.57, 0.67, 1.53);
}

.link-hover--bounce:hover::before,
.link-hover--bounce:focus-visible::before {
  opacity: 1;
  transform: scale3d(1.18, 0.1, 1);
  transition-duration: 0.4s;
  transition-timing-function: cubic-bezier(0.8, 0, 0.1, 1);
}

.link-hover--bounce span {
  display: inline-block;
  transform: translate3d(0, -4px, 0);
  transition: transform 0.2s 0.05s cubic-bezier(0.2, 0.57, 0.67, 1.53);
}

.link-hover--bounce:hover span,
.link-hover--bounce:focus-visible span {
  transform: translate3d(0, 0, 0);
  transition-delay: 0s;
  transition-duration: 0.4s;
  transition-timing-function: cubic-bezier(0.8, 0, 0.1, 1);
}

.link-hover__graphic {
  position: absolute;
  left: 0;
  top: 0;
  fill: none;
  stroke: currentColor;
  stroke-width: 1px;
  pointer-events: none;
}

.link-hover__graphic--stroke path {
  stroke-dasharray: 1;
  stroke-dashoffset: 1;
}

.link-hover:hover .link-hover__graphic--stroke path,
.link-hover:focus-visible .link-hover__graphic--stroke path {
  stroke-dashoffset: 0;
}

.link-hover--arc::before,
.link-hover--scribble::before {
  display: none;
}

.link-hover__graphic--arc {
  left: -23%;
  top: 73%;
}

.link-hover__graphic--arc path {
  transition: stroke-dashoffset 0.4s cubic-bezier(0.7, 0, 0.3, 1);
}

.link-hover:hover .link-hover__graphic--arc path,
.link-hover:focus-visible .link-hover__graphic--arc path {
  transition-duration: 0.3s;
  transition-timing-function: cubic-bezier(0.8, 1, 0.7, 1);
}

.link-hover__graphic--scribble {
  top: 100%;
}

.link-hover__graphic--scribble path {
  transition: stroke-dashoffset 0.6s cubic-bezier(0.7, 0, 0.3, 1);
}

.link-hover:hover .link-hover__graphic--scribble path,
.link-hover:focus-visible .link-hover__graphic--scribble path {
  transition-duration: 0.3s;
  transition-timing-function: cubic-bezier(0.8, 1, 0.7, 1);
}

@keyframes line-hover-line-up {
  0% {
    transform: scale3d(1, 0.045, 1);
    transform-origin: 50% 100%;
  }
  50% {
    transform: scale3d(1, 1, 1);
    transform-origin: 50% 100%;
  }
  51% {
    transform: scale3d(1, 1, 1);
    transform-origin: 50% 0%;
  }
  100% {
    transform: scale3d(1, 0.045, 1);
    transform-origin: 50% 0%;
  }
}

@keyframes line-hover-cover-up {
  0% {
    transform: scale3d(1, 0.045, 1);
    transform-origin: 50% 100%;
  }
  50% {
    transform: scale3d(1, 1, 1);
    transform-origin: 50% 100%;
  }
  51% {
    transform: scale3d(1, 1, 1);
    transform-origin: 50% 0%;
  }
  100% {
    transform: scale3d(1, 0.045, 1);
    transform-origin: 50% 0%;
  }
}

@media (prefers-reduced-motion: reduce) {
  .link-hover,
  .link-hover *,
  .link-hover::before,
  .link-hover::after {
    animation: none !important;
    transition-duration: 0.01ms !important;
  }
}
`;

/**
 * Line Hover Link Variants
 * 
 * slide    - Line slides in from right to left
 * double   - Two lines animate with different timings
 * grow     - Line grows thicker on hover
 * strike   - Strikethrough effect with text scale
 * fade     - Lines fade up with stagger delay
 * pulse    - Line pulses up and down
 * swap     - Two lines go opposite directions
 * sweep    - Full background cover sweep
 * bounce   - Bouncy squish animation
 * arc      - SVG arc stroke draws in
 * scribble - SVG scribble stroke draws in
 */
export type LineHoverVariant =
    | "slide"
    | "double"
    | "grow"
    | "strike"
    | "fade"
    | "pulse"
    | "swap"
    | "sweep"
    | "bounce"
    | "arc"
    | "scribble";

export interface LineHoverLinkProps
    extends React.AnchorHTMLAttributes<HTMLAnchorElement> {
    /** The animation variant */
    variant?: LineHoverVariant;
    /** Link content */
    children: React.ReactNode;
    /** Additional CSS classes */
    className?: string;
}

/**
 * SVG Arc Graphic - draws an arc stroke on hover
 */
const ArcGraphic = () => (
    <svg
        className="link-hover__graphic link-hover__graphic--stroke link-hover__graphic--arc"
        width="100%"
        height="18"
        viewBox="0 0 59 18"
    >
        <path
            d="M.945.149C12.3 16.142 43.573 22.572 58.785 10.842"
            pathLength="1"
        />
    </svg>
);

/**
 * SVG Scribble Graphic - draws a scribble stroke on hover
 */
const ScribbleGraphic = () => (
    <svg
        className="link-hover__graphic link-hover__graphic--stroke link-hover__graphic--scribble"
        width="100%"
        height="9"
        viewBox="0 0 101 9"
    >
        <path
            d="M.426 1.973C4.144 1.567 17.77-.514 21.443 1.48 24.296 3.026 24.844 4.627 27.5 7c3.075 2.748 6.642-4.141 10.066-4.688 7.517-1.2 13.237 5.425 17.59 2.745C58.5 3 60.464-1.786 66 2c1.996 1.365 3.174 3.737 5.286 4.41 5.423 1.727 25.34-7.981 29.14-1.294"
            pathLength="1"
        />
    </svg>
);

/**
 * LineHoverLink Component
 * 
 * A link component with beautiful underline hover animations.
 * Supports 11 different animation variants.
 * 
 * @example
 * ```tsx
 * <LineHoverLink variant="slide" href="/about">
 *   Learn more
 * </LineHoverLink>
 * ```
 */
export const LineHoverLink = React.forwardRef<
    HTMLAnchorElement,
    LineHoverLinkProps
>(({ variant = "slide", children, className, ...props }, ref) => {
    // Variants that need span wrapper for text animation
    const needsSpan = ["strike", "bounce", "arc", "scribble"].includes(variant);

    // Variants that need SVG graphics
    const svgVariant =
        variant === "arc" ? "arc" : variant === "scribble" ? "scribble" : null;

    return (
        <>
            <style>{lineHoverStyles}</style>
            <a
                ref={ref}
                className={cn("link-hover", `link-hover--${variant}`, className)}
                {...props}
            >
                {needsSpan ? <span>{children}</span> : children}
                {svgVariant === "arc" && <ArcGraphic />}
                {svgVariant === "scribble" && <ScribbleGraphic />}
            </a>
        </>
    );
});

LineHoverLink.displayName = "LineHoverLink";

export default LineHoverLink;

```

---

## `src\components\ui\liquid-gradient.tsx`

```tsx
'use client';
import { motion, AnimatePresence } from 'framer-motion';
type ColorKey =
  | 'color1'
  | 'color2'
  | 'color3'
  | 'color4'
  | 'color5'
  | 'color6'
  | 'color7'
  | 'color8'
  | 'color9'
  | 'color10'
  | 'color11'
  | 'color12'
  | 'color13'
  | 'color14'
  | 'color15'
  | 'color16'
  | 'color17';

export type Colors = Record<ColorKey, string>;

const svgOrder = [
  'svg1',
  'svg2',
  'svg3',
  'svg4',
  'svg3',
  'svg2',
  'svg1',
] as const;

type SvgKey = (typeof svgOrder)[number];

type Stop = {
  offset: number;
  stopColor: string;
};

type SvgState = {
  gradientTransform: string;
  stops: Stop[];
};

type SvgStates = Record<SvgKey, SvgState>;

const createStopsArray = (
  svgStates: SvgStates,
  svgOrder: readonly SvgKey[],
  maxStops: number
): Stop[][] => {
  let stopsArray: Stop[][] = [];
  for (let i = 0; i < maxStops; i++) {
    let stopConfigurations = svgOrder.map((svgKey) => {
      let svg = svgStates[svgKey];
      return svg.stops[i] || svg.stops[svg.stops.length - 1];
    });
    stopsArray.push(stopConfigurations);
  }
  return stopsArray;
};

type GradientSvgProps = {
  className: string;
  isHovered: boolean;
  colors: Colors;
};

const GradientSvg: React.FC<GradientSvgProps> = ({
  className,
  isHovered,
  colors,
}) => {
  const svgStates: SvgStates = {
    svg1: {
      gradientTransform:
        'translate(287.5 280) rotate(-29.0546) scale(689.807 1000)',
      stops: [
        { offset: 0, stopColor: colors.color1 },
        { offset: 0.188423, stopColor: colors.color2 },
        { offset: 0.260417, stopColor: colors.color3 },
        { offset: 0.328792, stopColor: colors.color4 },
        { offset: 0.328892, stopColor: colors.color5 },
        { offset: 0.328992, stopColor: colors.color1 },
        { offset: 0.442708, stopColor: colors.color6 },
        { offset: 0.537556, stopColor: colors.color7 },
        { offset: 0.631738, stopColor: colors.color1 },
        { offset: 0.725645, stopColor: colors.color8 },
        { offset: 0.817779, stopColor: colors.color9 },
        { offset: 0.84375, stopColor: colors.color10 },
        { offset: 0.90569, stopColor: colors.color1 },
        { offset: 1, stopColor: colors.color11 },
      ],
    },
    svg2: {
      gradientTransform:
        'translate(126.5 418.5) rotate(-64.756) scale(533.444 773.324)',
      stops: [
        { offset: 0, stopColor: colors.color1 },
        { offset: 0.104167, stopColor: colors.color12 },
        { offset: 0.182292, stopColor: colors.color13 },
        { offset: 0.28125, stopColor: colors.color1 },
        { offset: 0.328792, stopColor: colors.color4 },
        { offset: 0.328892, stopColor: colors.color5 },
        { offset: 0.453125, stopColor: colors.color6 },
        { offset: 0.515625, stopColor: colors.color7 },
        { offset: 0.631738, stopColor: colors.color1 },
        { offset: 0.692708, stopColor: colors.color8 },
        { offset: 0.75, stopColor: colors.color14 },
        { offset: 0.817708, stopColor: colors.color9 },
        { offset: 0.869792, stopColor: colors.color10 },
        { offset: 1, stopColor: colors.color1 },
      ],
    },
    svg3: {
      gradientTransform:
        'translate(264.5 339.5) rotate(-42.3022) scale(946.451 1372.05)',
      stops: [
        { offset: 0, stopColor: colors.color1 },
        { offset: 0.188423, stopColor: colors.color2 },
        { offset: 0.307292, stopColor: colors.color1 },
        { offset: 0.328792, stopColor: colors.color4 },
        { offset: 0.328892, stopColor: colors.color5 },
        { offset: 0.442708, stopColor: colors.color15 },
        { offset: 0.537556, stopColor: colors.color16 },
        { offset: 0.631738, stopColor: colors.color1 },
        { offset: 0.725645, stopColor: colors.color17 },
        { offset: 0.817779, stopColor: colors.color9 },
        { offset: 0.84375, stopColor: colors.color10 },
        { offset: 0.90569, stopColor: colors.color1 },
        { offset: 1, stopColor: colors.color11 },
      ],
    },
    svg4: {
      gradientTransform:
        'translate(860.5 420) rotate(-153.984) scale(957.528 1388.11)',
      stops: [
        { offset: 0.109375, stopColor: colors.color11 },
        { offset: 0.171875, stopColor: colors.color2 },
        { offset: 0.260417, stopColor: colors.color13 },
        { offset: 0.328792, stopColor: colors.color4 },
        { offset: 0.328892, stopColor: colors.color5 },
        { offset: 0.328992, stopColor: colors.color1 },
        { offset: 0.442708, stopColor: colors.color6 },
        { offset: 0.515625, stopColor: colors.color7 },
        { offset: 0.631738, stopColor: colors.color1 },
        { offset: 0.692708, stopColor: colors.color8 },
        { offset: 0.817708, stopColor: colors.color9 },
        { offset: 0.869792, stopColor: colors.color10 },
        { offset: 1, stopColor: colors.color11 },
      ],
    },
  };

  const maxStops = Math.max(
    ...Object.values(svgStates).map((svg) => svg.stops.length)
  );
  const stopsAnimationArray = createStopsArray(svgStates, svgOrder, maxStops);
  const gradientTransform = svgOrder.map(
    (svgKey) => svgStates[svgKey].gradientTransform
  );

  const variants = {
    hovered: {
      gradientTransform: gradientTransform,
      transition: { duration: 50, repeat: Infinity, ease: 'linear' as const },
    },
    notHovered: {
      gradientTransform: gradientTransform,
      transition: { duration: 10, repeat: Infinity, ease: 'linear' as const },
    },
  };

  return (
    <svg
      className={className}
      width='1030'
      height='280'
      viewBox='0 0 1030 280'
      fill='none'
      xmlns='http://www.w3.org/2000/svg'
    >
      <rect
        width='1030'
        height='280'
        rx='140'
        fill='url(#paint0_radial_905_231)'
      />
      <defs>
        <motion.radialGradient
          id='paint0_radial_905_231'
          cx='0'
          cy='0'
          r='1'
          gradientUnits='userSpaceOnUse'
          // @ts-ignore
          animate={isHovered ? variants.hovered : variants.notHovered}
        >
          {stopsAnimationArray.map((stopConfigs, index) => (
            <AnimatePresence key={index}>
              <motion.stop
                initial={{
                  offset: stopConfigs[0].offset,
                  stopColor: stopConfigs[0].stopColor,
                }}
                animate={{
                  offset: stopConfigs.map((config) => config.offset),
                  stopColor: stopConfigs.map((config) => config.stopColor),
                }}
                transition={{
                  duration: 0,
                  ease: 'linear',
                  repeat: Infinity,
                }}
              />
            </AnimatePresence>
          ))}
        </motion.radialGradient>
      </defs>
    </svg>
  );
};

type LiquidProps = {
  isHovered: boolean;
  colors: Colors;
  buttonType?: boolean;
};

export const Liquid: React.FC<LiquidProps> = ({
  isHovered,
  colors,
  buttonType,
}) => {
  return (
    <>
      {Array.from({ length: 7 }).map((_, index) => (
        <div
          key={index}
          className={`absolute ${
            index < 3 ? 'w-[443px] h-[121px]' : 'w-[756px] h-[207px]'
          } ${
            index === 0
              ? 'top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 mix-blend-difference'
              : index === 1
                ? 'top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 rotate-[164.971deg] mix-blend-difference'
                : index === 2
                  ? 'top-1/2 left-1/2 -translate-x-[53%] -translate-y-[53%] rotate-[-11.61deg] mix-blend-difference'
                  : index === 3
                    ? 'top-1/2 left-1/2 -translate-x-1/2 -translate-y-[57%] rotate-[-179.012deg] mix-blend-difference'
                    : index === 4
                      ? 'top-1/2 left-1/2 -translate-x-[57%] -translate-y-1/2 rotate-[-29.722deg] mix-blend-difference'
                      : index === 5
                        ? 'top-1/2 left-1/2 -translate-x-[62%] -translate-y-[24%] rotate-[160.227deg] mix-blend-difference'
                        : 'top-1/2 left-1/2 -translate-x-[67%] -translate-y-[29%] rotate-180 mix-blend-hard-light'
          }`}
        >
          <GradientSvg
            className='w-full h-full'
            isHovered={isHovered}
            colors={colors}
          />
        </div>
      ))}
    </>
  );
};

```

---

## `src\components\ui\liquid-metal.tsx`

```tsx
"use client";

import React, { memo, forwardRef } from "react";
import { LiquidMetal as LiquidMetalShader } from "@paper-design/shaders-react";
import { cn } from "@/lib/utils";

// ============================================================================
// LiquidMetal - Base shader wrapper component
// ============================================================================

export interface LiquidMetalProps {
    /** Base background color of the liquid metal */
    colorBack?: string;
    /** Tint/highlight color for the chrome effect */
    colorTint?: string;
    /** Animation speed (0.1 - 2.0 recommended) */
    speed?: number;
    /** Pattern complexity/repetition (1 - 10) */
    repetition?: number;
    /** Wave distortion amount (0 - 1) */
    distortion?: number;
    /** Texture scale */
    scale?: number;
    /** Additional CSS classes */
    className?: string;
    /** Inline styles */
    style?: React.CSSProperties;
}

export const LiquidMetal = memo(function LiquidMetal({
    colorBack = "#aaaaac",
    colorTint = "#ffffff",
    speed = 0.5,
    repetition = 4,
    distortion = 0.1,
    scale = 1,
    className,
    style,
}: LiquidMetalProps) {
    return (
        <div
            className={cn("absolute inset-0 z-0 overflow-hidden", className)}
            style={style}
        >
            <LiquidMetalShader
                colorBack={colorBack}
                colorTint={colorTint}
                speed={speed}
                repetition={repetition}
                distortion={distortion}
                softness={0}
                shiftRed={0.3}
                shiftBlue={-0.3}
                angle={45}
                shape="none"
                scale={scale}
                fit="cover"
                style={{ width: "100%", height: "100%" }}
            />
        </div>
    );
});

LiquidMetal.displayName = "LiquidMetal";

// ============================================================================
// LiquidMetalButton - Premium button with liquid metal border effect
// ============================================================================

export interface LiquidMetalButtonProps
    extends React.ButtonHTMLAttributes<HTMLButtonElement> {
    /** Button content */
    children: React.ReactNode;
    /** Optional icon displayed on the left */
    icon?: React.ReactNode;
    /** Border width in pixels */
    borderWidth?: number;
    /** Configuration for the LiquidMetal shader */
    metalConfig?: Omit<LiquidMetalProps, "className" | "style">;
    /** Size variant */
    size?: "sm" | "md" | "lg";
}

export const LiquidMetalButton = forwardRef<
    HTMLButtonElement,
    LiquidMetalButtonProps
>(
    (
        {
            children,
            icon,
            borderWidth = 4,
            metalConfig,
            size = "md",
            className,
            disabled,
            ...props
        },
        ref
    ) => {
        const sizeStyles = {
            sm: "py-2 pl-2 pr-6 gap-3 text-sm",
            md: "py-3 pl-3 pr-8 gap-4 text-base",
            lg: "py-4 pl-4 pr-10 gap-6 text-lg",
        };

        const iconSizes = {
            sm: "w-8 h-8",
            md: "w-10 h-10",
            lg: "w-12 h-12",
        };

        return (
            <button
                ref={ref}
                disabled={disabled}
                className={cn(
                    "relative group cursor-pointer border-none bg-transparent p-0 outline-none transition-transform active:scale-[0.98] disabled:opacity-50 disabled:cursor-not-allowed disabled:pointer-events-none",
                    className
                )}
                {...props}
            >
                <div
                    className="relative rounded-full overflow-hidden shadow-[0_20px_50px_-12px_rgba(0,0,0,0.25)]"
                    style={{ padding: borderWidth }}
                >
                    {/* Liquid Metal Border Layer */}
                    <LiquidMetal
                        colorBack={metalConfig?.colorBack ?? "#888888"}
                        colorTint={metalConfig?.colorTint ?? "#ffffff"}
                        speed={metalConfig?.speed ?? 0.4}
                        repetition={metalConfig?.repetition ?? 4}
                        distortion={metalConfig?.distortion ?? 0.15}
                        scale={metalConfig?.scale ?? 1}
                        className="absolute inset-0 z-0 rounded-full"
                    />

                    {/* Inner Button Body */}
                    <div
                        className={cn(
                            "relative z-10 rounded-full flex items-center",
                            "bg-white dark:bg-black",
                            "transition-colors duration-200",
                            "group-hover:bg-neutral-50 dark:group-hover:bg-neutral-900",
                            sizeStyles[size]
                        )}
                    >
                        {icon && (
                            <div
                                className={cn(
                                    "rounded-full flex items-center justify-center",
                                    "bg-neutral-100 dark:bg-neutral-800",
                                    "shadow-[inset_0_2px_4px_rgba(0,0,0,0.06)]",
                                    iconSizes[size]
                                )}
                            >
                                <span className="text-neutral-700 dark:text-neutral-300">
                                    {icon}
                                </span>
                            </div>
                        )}
                        <span className="font-medium tracking-tight text-neutral-900 dark:text-white">
                            {children}
                        </span>
                    </div>
                </div>
            </button>
        );
    }
);

LiquidMetalButton.displayName = "LiquidMetalButton";

export default LiquidMetalButton;

```

---

## `src\components\ui\liquid-ocean.tsx`

```tsx
"use client";

import React, { useRef, useMemo, useEffect } from "react";
import { Canvas, useFrame, useThree } from "@react-three/fiber";
import { RectAreaLightUniformsLib } from "three/examples/jsm/lights/RectAreaLightUniformsLib.js";
import * as THREE from "three";
import { cn } from "@/lib/utils";

// Initialize RectAreaLight uniforms
if (typeof window !== "undefined") {
    RectAreaLightUniformsLib.init();
}

// =============================================================================
// DEFAULT THEME (Original "Little Boxes" Style - DO NOT CHANGE)
// =============================================================================
const DEFAULT_THEME = {
    background: 0x000000,
    gridColor: 0x333333,
    accentColor: 0xF00589, // The original pinkish color
};

// =============================================================================
// OCEAN MESH COMPONENT
// =============================================================================
interface OceanMeshProps {
    geoSize: number;
    geoFragments: number;
    waveAmplitude: number;
    waveSpeed: number;
    accentColor: number;
    showWireframe: boolean;
    opacity: number;
}

function OceanMesh({
    geoSize,
    geoFragments,
    waveAmplitude,
    waveSpeed,
    accentColor,
    showWireframe,
    opacity,
}: OceanMeshProps) {
    const meshRef = useRef<THREE.Mesh>(null);
    const wireRef = useRef<THREE.Mesh>(null);

    const { geometry, waves } = useMemo(() => {
        const geo = new THREE.PlaneGeometry(geoSize, geoSize, geoFragments, geoFragments);
        const positionAttribute = geo.getAttribute("position");
        const waveData: Array<{
            x: number;
            y: number;
            z: number;
            ang: number;
            amp: number;
            speed: number;
        }> = [];

        for (let i = 0; i < positionAttribute.count; i++) {
            waveData.push({
                x: positionAttribute.getX(i),
                y: positionAttribute.getY(i),
                z: positionAttribute.getZ(i),
                ang: Math.PI * 2,
                amp: Math.random() * waveAmplitude,
                speed: 0.03 + Math.random() * waveSpeed,
            });
        }

        return { geometry: geo, waves: waveData };
    }, [geoSize, geoFragments, waveAmplitude, waveSpeed]);

    useFrame(() => {
        if (!meshRef.current) return;

        const positionAttribute = meshRef.current.geometry.getAttribute("position");

        for (let i = 0; i < positionAttribute.count; i++) {
            const wave = waves[i];
            positionAttribute.setX(i, wave.x + Math.cos(wave.ang) * wave.amp);
            positionAttribute.setY(i, wave.y + Math.sin(wave.ang / 2) * wave.amp);
            positionAttribute.setZ(i, wave.z + Math.cos(wave.ang / 3) * wave.amp);
            wave.ang += wave.speed;
        }

        positionAttribute.needsUpdate = true;
    });

    const wireframeMaterial = useMemo(
        () =>
            new THREE.MeshPhysicalMaterial({
                color: accentColor,
                wireframe: true,
                transparent: false,
                opacity: 1,
            }),
        [accentColor]
    );

    const surfaceMaterial = useMemo(
        () =>
            new THREE.MeshPhysicalMaterial({
                color: accentColor,
                transparent: true,
                opacity: opacity,
                wireframe: false,
            }),
        [accentColor, opacity]
    );

    return (
        <group rotation={[-90 * Math.PI / 180, 0, 0]}>
            <mesh ref={meshRef} geometry={geometry} material={surfaceMaterial} receiveShadow />
            {showWireframe && (
                <mesh ref={wireRef} geometry={geometry} material={wireframeMaterial} />
            )}
        </group>
    );
}

// =============================================================================
// BOAT / FLOATING BOXES COMPONENT
// =============================================================================
interface BoatData {
    position: [number, number, number];
    scale: [number, number, number];
    rotationY: number;
    vel: number;
    amp: number;
    pos: number;
}

function Boat({ data, color }: { data: BoatData; color: number }) {
    const meshRef = useRef<THREE.Mesh>(null);

    useFrame(({ clock }) => {
        if (!meshRef.current) return;
        const time = clock.getElapsedTime() * 3;

        meshRef.current.rotation.z = (Math.sin(time / data.vel) * data.amp * Math.PI) / 180;
        meshRef.current.rotation.x = (Math.cos(time) * data.vel * Math.PI) / 180;
        meshRef.current.position.y = Math.sin(time / data.vel) * data.pos;
    });

    return (
        <mesh
            ref={meshRef}
            position={data.position}
            rotation={[0, data.rotationY, 0]}
            scale={data.scale}
            castShadow
        >
            <boxGeometry args={[1, 1, 1]} />
            <meshStandardMaterial color={color} />
        </mesh>
    );
}

function BoatGroup({ count, spreadRange, color }: { count: number; spreadRange: number; color: number }) {
    const boats = useMemo(() => {
        const items: BoatData[] = [];
        for (let i = 0; i < count; i++) {
            const x = -Math.random() * spreadRange + Math.random() * spreadRange;
            const z = -Math.random() * spreadRange + Math.random() * spreadRange;
            const sX = Math.random();
            const sY = 0.5 + Math.random() * 2;

            items.push({
                position: [x, 0, z],
                scale: [sX, sY, sX],
                rotationY: (Math.random() * 360 * Math.PI) / 180,
                vel: 1 + Math.random() * 4,
                amp: 1 + Math.random() * 6,
                pos: Math.random() * 0.2,
            });
        }
        return items;
    }, [count, spreadRange]);

    return (
        <group>
            {boats.map((boat, i) => (
                <Boat key={i} data={boat} color={color} />
            ))}
        </group>
    );
}

// =============================================================================
// SCENE CONTENT
// =============================================================================
interface SceneContentProps {
    backgroundColor: number;
    gridColor: number;
    accentColor: number;
    rotationSpeed: number;
    showGrid: boolean;
    showBoats: boolean;
    boatCount: number;
    boatSpread: number;
    oceanSize: number;
    oceanFragments: number;
    waveAmplitude: number;
    waveSpeed: number;
    showWireframe: boolean;
    oceanOpacity: number;
}

function SceneContent({
    backgroundColor,
    gridColor,
    accentColor,
    rotationSpeed,
    showGrid,
    showBoats,
    boatCount,
    boatSpread,
    oceanSize,
    oceanFragments,
    waveAmplitude,
    waveSpeed,
    showWireframe,
    oceanOpacity,
}: SceneContentProps) {
    const { scene, camera } = useThree();
    const rectLightRef = useRef<THREE.RectAreaLight>(null);
    const groupRef = useRef<THREE.Group>(null);

    useEffect(() => {
        scene.fog = new THREE.Fog(backgroundColor, 5, 20);
        scene.background = new THREE.Color(backgroundColor);
    }, [scene, backgroundColor]);

    useFrame(() => {
        camera.lookAt(0, 0, 0);
        if (rectLightRef.current) {
            rectLightRef.current.lookAt(0, 0, 0);
        }
        if (groupRef.current) {
            groupRef.current.rotation.y += rotationSpeed;
        }
    });

    return (
        <>
            {/* Lighting - Original setup */}
            <hemisphereLight args={[0xFFD3D3, accentColor, 2]} />
            <pointLight args={[accentColor, 1]} position={[-5, -20, -20]} />
            <rectAreaLight
                ref={rectLightRef}
                args={[accentColor, 20, 3, 3]}
                position={[2, 2, -20]}
            />
            <pointLight args={[accentColor, 0.1]} position={[0, 2, -2]} />

            {/* Rotating Group */}
            <group ref={groupRef}>
                {showGrid && <gridHelper args={[20, 20]} position={[0, -1, 0]} />}
                {showBoats && <BoatGroup count={boatCount} spreadRange={boatSpread} color={accentColor} />}
                <OceanMesh
                    geoSize={oceanSize}
                    geoFragments={oceanFragments}
                    waveAmplitude={waveAmplitude}
                    waveSpeed={waveSpeed}
                    accentColor={accentColor}
                    showWireframe={showWireframe}
                    opacity={oceanOpacity}
                />
            </group>
        </>
    );
}

// =============================================================================
// MAIN COMPONENT - Props for reusability, defaults match original exactly
// =============================================================================
export interface LiquidOceanProps {
    /** Additional CSS classes */
    className?: string;
    /** Background color as hex number (default: 0x000000 - black) */
    backgroundColor?: number;
    /** Grid line color as hex number (default: 0x333333) */
    gridColor?: number;
    /** Accent color for ocean, boats, lights as hex number (default: 0xF00589 - pink) */
    accentColor?: number;
    /** Camera field of view (default: 20) */
    fov?: number;
    /** Scene rotation speed per frame (default: 0.001) */
    rotationSpeed?: number;
    /** Show grid helper (default: true) */
    showGrid?: boolean;
    /** Show floating boxes/boats (default: true) */
    showBoats?: boolean;
    /** Number of floating boxes (default: 5) */
    boatCount?: number;
    /** Spread range for floating boxes (default: 5) */
    boatSpread?: number;
    /** Ocean plane size (default: 25) */
    oceanSize?: number;
    /** Ocean geometry fragments/subdivisions (default: 25) */
    oceanFragments?: number;
    /** Maximum wave amplitude (default: 0.2) */
    waveAmplitude?: number;
    /** Wave animation speed multiplier (default: 0.05) */
    waveSpeed?: number;
    /** Show wireframe overlay on ocean (default: true) */
    showWireframe?: boolean;
    /** Ocean surface opacity (default: 0.85) */
    oceanOpacity?: number;
    /** Overlay content (e.g., title text) */
    children?: React.ReactNode;
}

export function LiquidOcean({
    className,
    // Original colors - DO NOT CHANGE DEFAULTS
    backgroundColor = DEFAULT_THEME.background,
    gridColor = DEFAULT_THEME.gridColor,
    accentColor = DEFAULT_THEME.accentColor,
    // Original settings - DO NOT CHANGE DEFAULTS
    fov = 20,
    rotationSpeed = 0.001,
    showGrid = true,
    showBoats = true,
    boatCount = 5,
    boatSpread = 5,
    oceanSize = 25,
    oceanFragments = 25,
    waveAmplitude = 0.2,
    waveSpeed = 0.05,
    showWireframe = true,
    oceanOpacity = 0.85,
    children,
}: LiquidOceanProps) {
    const [isVisible, setIsVisible] = React.useState(false);
    const containerRef = useRef<HTMLDivElement>(null);

    useEffect(() => {
        const observer = new IntersectionObserver(
            ([entry]) => {
                setIsVisible(entry.isIntersecting);
            },
            { threshold: 0 }
        );
        if (containerRef.current) observer.observe(containerRef.current);
        return () => observer.disconnect();
    }, []);

    return (
        <div ref={containerRef} className={cn("relative w-full h-full min-h-[400px] overflow-hidden bg-black cursor-crosshair", className)}>
            <Canvas
                shadows
                dpr={1}
                frameloop={isVisible ? "always" : "never"}
                camera={{ position: [0, 2, 10], fov }}
                gl={{ antialias: false, alpha: false }}
                style={{ position: "absolute", inset: 0 }}
            >
                <SceneContent
                    backgroundColor={backgroundColor}
                    gridColor={gridColor}
                    accentColor={accentColor}
                    rotationSpeed={rotationSpeed}
                    showGrid={showGrid}
                    showBoats={showBoats}
                    boatCount={boatCount}
                    boatSpread={boatSpread}
                    oceanSize={oceanSize}
                    oceanFragments={isVisible ? oceanFragments : 5}
                    waveAmplitude={waveAmplitude}
                    waveSpeed={waveSpeed}
                    showWireframe={showWireframe}
                    oceanOpacity={oceanOpacity}
                />
            </Canvas>

            {/* Overlay Content */}
            {children && (
                <div className="absolute inset-0 pointer-events-none select-none">
                    {children}
                </div>
            )}
        </div>
    );
}

export default LiquidOcean;

```

---

## `src\components\ui\liquid-text.tsx`

```tsx
"use client";

import { useEffect, useRef } from "react";
import * as THREE from "three";
import { cn } from "@/lib/utils";

interface LiquidTextProps {
    /** Text to display */
    text?: string;
    /** Font size in pixels */
    fontSize?: number;
    /** Font family */
    font?: string;
    /** Fixed text color (overrides theme colors) */
    color?: string;
    /** Text color in light mode */
    lightColor?: string;
    /** Text color in dark mode */
    darkColor?: string;
    /** Additional CSS classes */
    className?: string;
}

const createTextTexture = (text: string, size: number, font: string, color: string): THREE.Texture => {
    const canvas = document.createElement("canvas");
    canvas.width = 2048;
    canvas.height = 2048;
    const ctx = canvas.getContext("2d");
    if (!ctx) return new THREE.CanvasTexture(canvas);

    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.font = `bold ${size}px ${font}`;
    ctx.fillStyle = color;
    ctx.textAlign = "center";
    ctx.textBaseline = "middle";
    ctx.fillText(text, canvas.width / 2, canvas.height / 2);

    const texture = new THREE.CanvasTexture(canvas);
    texture.needsUpdate = true;
    return texture;
};

const vertexShader = `
    varying vec2 vUv;
    uniform vec3 uDisplacement;

    float easeInOutCubic(float x) {
        return x < 0.5 ? 4.0 * x * x * x : 1.0 - pow(-2.0 * x + 2.0, 3.0) / 2.0;
    }

    float map(float value, float min1, float max1, float min2, float max2) {
        return min2 + (value - min1) * (max2 - min2) / (max1 - min1);
    }

    void main() {
        vUv = uv;
        vec3 displaced = position;
        vec4 worldPosition = modelMatrix * vec4(position, 1.0);
        float dist = length(uDisplacement - worldPosition.rgb);
        float minDistance = 3.0;

        if (dist < minDistance) {
            float mapped = map(dist, 0.0, minDistance, 1.0, 0.0);
            displaced.z += easeInOutCubic(mapped);
        }

        gl_Position = projectionMatrix * modelViewMatrix * vec4(displaced, 1.0);
    }
`;

const fragmentShader = `
    varying vec2 vUv;
    uniform sampler2D uTexture;

    void main() {
        gl_FragColor = texture2D(uTexture, vUv);
    }
`;

export function LiquidText({
    text = "Liquid Text",
    fontSize = 200,
    font = "Inter, sans-serif",
    color,
    lightColor = "#000000",
    darkColor = "#ffffff",
    className,
}: LiquidTextProps) {
    const containerRef = useRef<HTMLDivElement>(null);

    useEffect(() => {
        const container = containerRef.current;
        if (!container) return;

        const rect = container.getBoundingClientRect();
        const width = rect.width || 1;
        const height = rect.height || 1;
        if (height === 0) return;

        const scene = new THREE.Scene();
        scene.background = null;

        const cameraDistance = 8;
        const aspect = width / height;
        const camera = new THREE.OrthographicCamera(
            -cameraDistance * aspect, cameraDistance * aspect,
            cameraDistance, -cameraDistance, 0.01, 1000
        );
        camera.position.set(0, -10, 5);
        camera.lookAt(0, 0, 0);

        let renderer: THREE.WebGLRenderer;
        try {
            renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
        } catch {
            const fallback = document.createElement("div");
            fallback.className = "flex h-full w-full items-center justify-center text-5xl font-bold text-neutral-950 dark:text-white md:text-7xl";
            fallback.textContent = text;
            container.appendChild(fallback);

            return () => {
                fallback.remove();
            };
        }

        renderer.setClearColor(0x000000, 0);
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
        renderer.setSize(width, height, false);
        renderer.domElement.style.width = "100%";
        renderer.domElement.style.height = "100%";
        container.appendChild(renderer.domElement);

        const geometry = new THREE.PlaneGeometry(15, 15, 100, 100);
        const getActiveColor = () => color || (document.documentElement.classList.contains("dark") ? darkColor : lightColor);

        let currentColor = getActiveColor();
        let textTexture = createTextTexture(text, fontSize, font, currentColor);

        const shaderMaterial = new THREE.ShaderMaterial({
            uniforms: {
                uTexture: { value: textTexture },
                uDisplacement: { value: new THREE.Vector3(0, 0, 0) },
            },
            vertexShader,
            fragmentShader,
            transparent: true,
            depthWrite: false,
            side: THREE.DoubleSide,
        });

        const plane = new THREE.Mesh(geometry, shaderMaterial);
        plane.rotation.z = Math.PI / 4;
        scene.add(plane);

        const hitPlaneGeometry = new THREE.PlaneGeometry(500, 500);
        const hitPlaneMaterial = new THREE.MeshBasicMaterial({ transparent: true, opacity: 0 });
        const hitPlane = new THREE.Mesh(hitPlaneGeometry, hitPlaneMaterial);
        scene.add(hitPlane);

        const raycaster = new THREE.Raycaster();
        const pointer = new THREE.Vector2();

        const onPointerMove = (e: PointerEvent) => {
            const bounds = container.getBoundingClientRect();
            pointer.x = ((e.clientX - bounds.left) / bounds.width) * 2 - 1;
            pointer.y = -((e.clientY - bounds.top) / bounds.height) * 2 + 1;
            raycaster.setFromCamera(pointer, camera);
            const [hit] = raycaster.intersectObject(hitPlane);
            if (hit) (shaderMaterial.uniforms.uDisplacement.value as THREE.Vector3).copy(hit.point);
        };

        container.addEventListener("pointermove", onPointerMove);

        const handleResize = () => {
            const r = container.getBoundingClientRect();
            if (r.height === 0) return;
            const a = r.width / r.height;
            camera.left = -cameraDistance * a;
            camera.right = cameraDistance * a;
            camera.updateProjectionMatrix();
            renderer.setSize(r.width, r.height, false);
        };

        window.addEventListener("resize", handleResize);

        let animationId = 0;
        const render = () => {
            animationId = requestAnimationFrame(render);
            renderer.render(scene, camera);
        };
        render();

        const observer = new MutationObserver(() => {
            const next = getActiveColor();
            if (next !== currentColor) {
                const tex = createTextTexture(text, fontSize, font, next);
                shaderMaterial.uniforms.uTexture.value = tex;
                textTexture.dispose();
                textTexture = tex;
                currentColor = next;
            }
        });
        if (!color) observer.observe(document.documentElement, { attributes: true, attributeFilter: ["class"] });

        return () => {
            window.removeEventListener("resize", handleResize);
            container.removeEventListener("pointermove", onPointerMove);
            cancelAnimationFrame(animationId);
            observer.disconnect();
            if (renderer.domElement.parentNode === container) container.removeChild(renderer.domElement);
            renderer.renderLists.dispose();
            renderer.dispose();
            renderer.forceContextLoss();
            textTexture.dispose();
            geometry.dispose();
            hitPlaneGeometry.dispose();
            hitPlaneMaterial.dispose();
            shaderMaterial.dispose();
        };
    }, [text, fontSize, font, color, lightColor, darkColor]);

    return <div ref={containerRef} className={cn("relative w-full h-[600px]", className)} />;
}

export default LiquidText;

```

---

## `src\components\ui\logo-slider.tsx`

```tsx
"use client";

import * as React from "react";
import { cn } from "@/lib/utils";

/* =============================================================================
   LogoSlider Component
   
   A smooth, infinite marquee/slider component for displaying logos.
   Uses Tailwind CSS where possible, raw CSS only for animations/masks.
============================================================================= */

export interface LogoSliderProps {
    /** Array of React nodes (logos, icons, images) to display */
    logos: React.ReactNode[];
    /** Animation speed - higher = slower (default: 60) */
    speed?: number;
    /** Scroll direction. Default: "left" */
    direction?: "left" | "right";
    /** Whether to show blur overlays on edges. Default: true */
    showBlur?: boolean;
    /** Number of blur layers for progressive effect. Default: 8 */
    blurLayers?: number;
    /** Blur intensity multiplier. Default: 1 */
    blurIntensity?: number;
    /** Additional CSS classes */
    className?: string;
    /** Whether to pause animation on hover. Default: false */
    pauseOnHover?: boolean;
}

/**
 * LogoSlider Component
 *
 * A beautiful infinite marquee for showcasing logos, partners, or any content.
 * Uses per-item CSS animations for optimal performance.
 */


export const LogoSlider = ({
    logos,
    speed = 60,
    direction = "left",
    showBlur = true,
    blurLayers = 8,
    blurIntensity = 1,
    className,
    pauseOnHover = false,
}: LogoSliderProps) => {
    return (
        <div
            className={cn(
                "logo-slider w-full overflow-hidden",
                className
            )}
            style={
                {
                    "--speed": speed,
                    "--count": logos.length,
                    "--blurs": blurLayers,
                    "--blur": blurIntensity,
                } as React.CSSProperties
            }
        >
            <div
                className={cn(
                    "logo-slider__container",
                    "relative w-full min-h-[80px] grid"
                )}
                data-direction={direction}
                data-pause-on-hover={pauseOnHover}
            >
                {/* Progressive Blur Overlay - Left */}
                {showBlur && (
                    <div className="logo-slider__blur logo-slider__blur--left absolute top-0 bottom-0 left-0 w-1/4 z-10 pointer-events-none rotate-180">
                        {Array.from({ length: blurLayers }).map((_, i) => (
                            <div
                                key={`blur-left-${i}`}
                                className="absolute inset-0"
                                style={{ "--blur-index": i } as React.CSSProperties}
                            />
                        ))}
                    </div>
                )}

                {/* Progressive Blur Overlay - Right */}
                {showBlur && (
                    <div className="logo-slider__blur logo-slider__blur--right absolute top-0 bottom-0 right-0 w-1/4 z-10 pointer-events-none">
                        {Array.from({ length: blurLayers }).map((_, i) => (
                            <div
                                key={`blur-right-${i}`}
                                className="absolute inset-0"
                                style={{ "--blur-index": i } as React.CSSProperties}
                            />
                        ))}
                    </div>
                )}

                {/* Logo Track */}
                <ul className="logo-slider__track flex items-center h-full w-fit m-0 p-0 list-none">
                    {logos.map((logo, index) => (
                        <li
                            key={index}
                            className="logo-slider__item h-4/5 w-[120px] sm:w-[140px] lg:w-[160px] aspect-video grid place-items-center shrink-0"
                            style={{ "--item-index": index } as React.CSSProperties}
                        >
                            <div className="w-full h-full flex items-center justify-center [&>svg]:h-[65%] [&>svg]:w-auto [&>svg]:fill-zinc-800 dark:[&>svg]:fill-zinc-200 [&>img]:h-[65%] [&>img]:w-auto [&>img]:object-contain [&>img]:grayscale [&>img]:brightness-50 dark:[&>img]:brightness-125">
                                {logo}
                            </div>
                        </li>
                    ))}
                </ul>
            </div>
        </div>
    );
};

LogoSlider.displayName = "LogoSlider";

export default LogoSlider;

```

---

## `src\components\ui\magnetic-spotlight-marquee.tsx`

```tsx
"use client";

import React, { useRef, useState, useEffect, ReactNode } from "react";
import gsap from "gsap";
import { cn } from "@/lib/utils";

interface MagneticSpotlightMarqueeProps {
  className?: string;
  images?: string[];
  title?: string[];
  subtitle?: string[];
  paragraphs?: string[][];
  navEmail?: string;
  navLinks?: string;
  footerText?: string;
}

const config = {
  marqueeScrollSpeed: 180, // Increased for a faster, dynamic feel
  stripFollowEase: 0.05,
  stripEdgeInset: 175,
  contentRiseRate: 0.85,
  risenTopGap: 100,
  liftHeadStart: 125,
  wakeStrength: 2.5,
  wakeReach: 125,
  lineSettleEase: 0.09,
};

const DEFAULT_IMAGES = [
  "https://images.unsplash.com/photo-1578632767115-351597cf2477?q=80&w=800&auto=format&fit=crop", 
  "https://images.unsplash.com/photo-1607604276583-eef5d076aa5f?q=80&w=800&auto=format&fit=crop", 
  "https://images.unsplash.com/photo-1541562232579-512a21360020?q=80&w=800&auto=format&fit=crop", 
  "https://images.unsplash.com/photo-1581833971358-2c8b550f87b3?q=80&w=800&auto=format&fit=crop", 
  "https://images.unsplash.com/photo-1560972550-aba3456b5564?q=80&w=800&auto=format&fit=crop", 
  "https://images.unsplash.com/photo-1613376023733-0a73315d9b06?q=80&w=800&auto=format&fit=crop",
];

const DEFAULT_TITLE = ["VengeanceUI"];
const DEFAULT_SUBTITLE = ["BUILD FASTER", "SHIP BETTER"];
const DEFAULT_PARAGRAPHS = [
  [
    "Vengeance UI is a premium component library",
    "specializing in smooth animations, interactive",
    "interfaces, and modern design.",
  ],
  [
    "We prioritize developer experience and aesthetics.",
    "Our components span across complex interactions,",
    "3D elements, and smooth animations built",
    "for React and modern frameworks. Our library is tailored",
    "to distinct challenges within modern web development."
  ]
];

export function MagneticSpotlightMarquee({
  className,
  images = DEFAULT_IMAGES,
  title = DEFAULT_TITLE,
  subtitle = DEFAULT_SUBTITLE,
  paragraphs = DEFAULT_PARAGRAPHS,
  navEmail = "hello@vengeance.ui",
  navLinks = "Documentation, Components, GitHub",
  footerText = "We navigate in no-nonsense environments pushing the boundaries of web design. Whether you're a startup or a global leader, building a new identity or interactive platform, Vengeance UI is your partner in innovation. Our premium components ensure that every project feels magical, collaborative, and smooth.",
}: MagneticSpotlightMarqueeProps) {
  const containerRef = useRef<HTMLDivElement>(null);
  const marqueeStripRef = useRef<HTMLDivElement>(null);
  const marqueeTrackRef = useRef<HTMLDivElement>(null);
  const contentWrapperRef = useRef<HTMLDivElement>(null);

  // State to hold cloned images to fill width
  const [clonedImages, setClonedImages] = useState<string[]>(images);

  useEffect(() => {
    if (!marqueeTrackRef.current || !marqueeStripRef.current || !containerRef.current || !contentWrapperRef.current) return;

    const marqueeTrack = marqueeTrackRef.current;

    // 1. Setup infinite horizontal marquee with GSAP
    // Calculate width statically to avoid issues with unloaded images
    const isMobile = window.innerWidth < 768;
    const itemWidth = isMobile ? 140 : 180; // Smaller, square width
    const gap = 16; // 1rem gap
    const oneSetWidth = images.length * (itemWidth + gap);
    const setsNeeded = Math.ceil(window.innerWidth / oneSetWidth) + 2;
    
    const newImages = [];
    for (let i = 0; i < setsNeeded; i++) {
      newImages.push(...images);
    }
    setClonedImages(newImages);

    // Wait for React to render clones, then animate
    const ctx = gsap.context(() => {
      setTimeout(() => {
         gsap.to(marqueeTrack, {
           x: `-${oneSetWidth}px`,
           duration: oneSetWidth / 600, // Hardcoded even faster speed (600)
           ease: "none",
           repeat: -1,
           modifiers: {
             x: (x) => `${gsap.utils.wrap(-oneSetWidth, 0, parseFloat(x))}px`
           }
         });
      }, 100);
    }, marqueeTrack);

    return () => ctx.revert();
  }, [images]);

  // Wake effect logic
  useEffect(() => {
    if (!containerRef.current || !marqueeStripRef.current || !contentWrapperRef.current) return;

    const spotlightSection = containerRef.current;
    const marqueeStrip = marqueeStripRef.current;

    let stripBaseTop = 0;
    let stripHeight = 0;
    let sectionHeight = 0;
    let stripRestCenterY = 0;
    let contentTopAtRest = 0;

    let stripTargetY = 0;
    let stripCurrentY = 0;
    let stripPrevY = 0;
    let hasPointerMoved = false;

    let targets: { el: HTMLElement; restCenterY: number; currentY: number }[] = [];
    let rafId: number;

    const measureGeometry = () => {
      sectionHeight = spotlightSection.getBoundingClientRect().height;
      stripBaseTop = marqueeStrip.offsetTop;
      stripHeight = marqueeStrip.offsetHeight;
      
      stripRestCenterY = config.stripEdgeInset;
      
      const elements = Array.from(spotlightSection.querySelectorAll('.wake-target')) as HTMLElement[];
      
      let blockTop = Infinity;
      targets = elements.map(el => {
        let y = 0;
        let node: HTMLElement | null = el;
        while (node && node !== spotlightSection) {
          y += node.offsetTop;
          node = node.offsetParent as HTMLElement;
        }
        const restCenterY = y + el.offsetHeight / 2;
        blockTop = Math.min(blockTop, restCenterY - el.offsetHeight / 2);
        
        return {
          el,
          restCenterY,
          currentY: 0
        };
      });

      contentTopAtRest = isFinite(blockTop) ? blockTop : sectionHeight * 0.4;
      
      if (!hasPointerMoved) {
        const restY = config.stripEdgeInset - stripHeight / 2;
        stripTargetY = restY;
        stripCurrentY = restY;
        stripPrevY = restY;
        gsap.set(marqueeStrip, { y: stripCurrentY });
      }
    };

    setTimeout(measureGeometry, 100);
    window.addEventListener('resize', measureGeometry);

    const handlePointerMove = (e: MouseEvent) => {
      hasPointerMoved = true;
      const rect = spotlightSection.getBoundingClientRect();
      const pointerY = e.clientY - rect.top;
      stripTargetY = pointerY - stripHeight / 2;
    };

    const handlePointerLeave = () => {
      hasPointerMoved = false;
      stripTargetY = config.stripEdgeInset - stripHeight / 2;
    };

    spotlightSection.addEventListener('mousemove', handlePointerMove);
    spotlightSection.addEventListener('mouseleave', handlePointerLeave);

    const render = () => {
      stripCurrentY += (stripTargetY - stripCurrentY) * config.stripFollowEase;
      gsap.set(marqueeStrip, { y: stripCurrentY });

      const stripCenterY = stripBaseTop + stripCurrentY + stripHeight / 2;
      const stripVelocityY = stripCurrentY - stripPrevY;
      stripPrevY = stripCurrentY;

      const descentBelowRest = Math.max(0, stripCenterY - stripRestCenterY);
      const maxRise = Math.max(0, contentTopAtRest - config.risenTopGap);
      const contentRise = -Math.min(
        descentBelowRest * config.contentRiseRate,
        maxRise
      );

      targets.forEach(line => {
        const gapToStrip = line.restCenterY - stripCenterY;
        const reachedLine = stripCenterY + config.liftHeadStart >= line.restCenterY;
        
        const wakeInfluence = Math.exp(
          -(gapToStrip * gapToStrip) / (2 * config.wakeReach * config.wakeReach)
        );
        const wakeOffset = stripVelocityY * wakeInfluence * config.wakeStrength;
        
        const lineTarget = (reachedLine ? contentRise : 0) + wakeOffset;
        
        line.currentY += (lineTarget - line.currentY) * config.lineSettleEase;
        gsap.set(line.el, { y: line.currentY });
      });

      rafId = requestAnimationFrame(render);
    };
    rafId = requestAnimationFrame(render);

    return () => {
      window.removeEventListener('resize', measureGeometry);
      spotlightSection.removeEventListener('mousemove', handlePointerMove);
      spotlightSection.removeEventListener('mouseleave', handlePointerLeave);
      cancelAnimationFrame(rafId);
    };
  }, []);

  return (
    <section
      ref={containerRef}
      className={cn(
        "spotlight relative w-full h-[100vh] min-h-[800px] overflow-hidden bg-white dark:bg-[#0f0f0f] text-white font-sans",
        className
      )}
      style={{ fontFamily: "'Instrument Sans', sans-serif" }}
    >
      {/* Top Nav - Centered layout as seen in screenshot */}
      <div className="absolute top-0 left-0 w-full p-6 flex flex-col items-center justify-center z-50 text-[10px] md:text-xs font-medium tracking-wide opacity-90 mix-blend-difference pointer-events-none">
        <div>{navEmail}</div>
        <div>{navLinks}</div>
      </div>

      {/* Marquee Strip */}
      <div 
        ref={marqueeStripRef} 
        className="spotlight-marquee absolute left-0 w-full z-20 h-[160px] md:h-[200px] pointer-events-none"
        style={{ top: 0 }} 
      >
        <div 
          ref={marqueeTrackRef} 
          className="spotlight-marquee-track flex gap-4 h-full items-center absolute top-0 left-0"
        >
          {clonedImages.map((img, idx) => (
            <div key={idx} className="w-[140px] h-[140px] md:w-[180px] md:h-[180px] shrink-0 rounded-[20px] overflow-hidden shadow-sm bg-neutral-100 dark:bg-neutral-900">
              <img
                src={img}
                alt="Marquee item"
                className="w-full h-full object-cover"
                loading="lazy"
              />
            </div>
          ))}
        </div>
      </div>

      {/* Main Content Layout */}
      <div 
        ref={contentWrapperRef}
        className="spotlight-content-wrapper relative w-full h-full flex flex-col items-center justify-center px-6 md:px-12 lg:px-24 z-30 pointer-events-none mix-blend-difference"
      >
        {/* Title */}
        <h1 
          className="text-[15vw] md:text-[12rem] font-normal leading-[0.85] tracking-tighter mb-20 text-center flex flex-col items-center"
          style={{ fontFamily: "'Instrument Serif', serif" }}
        >
          {title.map((line, idx) => (
            <div key={idx} className="wake-target inline-block relative">
              {line}
              {/* Optional playful dot for 'Studio' to mimic the screenshot */}
              {line === "Studio" && (
                <span className="absolute right-[0.45em] top-[0.1em] w-[0.25em] h-[0.25em] bg-white rounded-full"></span>
              )}
            </div>
          ))}
        </h1>
        
        {/* Subtitle & Paragraphs row */}
        <div className="w-full max-w-7xl mx-auto flex flex-col md:flex-row justify-between items-start mt-8 px-4 md:px-8 gap-8 md:gap-4">
          
          {/* Subtitle / Header (Left side) */}
          <div className="flex-1 md:max-w-[280px] text-right md:text-right mt-1">
            <h3 className="text-xl md:text-3xl uppercase tracking-tight font-medium leading-[1.1]">
              {subtitle.map((line, idx) => (
                <div key={idx} className="wake-target">{line}</div>
              ))}
            </h3>
          </div>

          {/* Paragraphs (Right side) */}
          <div className="flex-1 flex flex-col sm:flex-row gap-6 md:gap-12 text-[10px] md:text-xs leading-[1.6]">
            {paragraphs.map((para, pIdx) => (
              <div key={pIdx} className="flex-1 flex flex-col">
                {para.map((line, lIdx) => (
                  <div key={lIdx} className="wake-target whitespace-nowrap">
                    {line}
                  </div>
                ))}
              </div>
            ))}
          </div>

        </div>
      </div>

      {/* Footer */}
      <div className="absolute bottom-0 left-0 w-full p-8 z-40 flex justify-center pointer-events-none mix-blend-difference">
        <p className="text-[8px] md:text-[10px] text-white/70 max-w-2xl text-center leading-[1.6]">
          {footerText}
        </p>
      </div>
    </section>
  );
}

export default MagneticSpotlightMarquee;

```

---

## `src\components\ui\masked-avatars.tsx`

```tsx
"use client"
import React, { useState } from "react"
import { cn } from "@/lib/utils"
import { motion } from "framer-motion"

interface Avatar {
    avatar: string
    name: string
}

export interface MaskedAvatarsProps {
    avatars?: Avatar[]
    size?: number // avatar size (responsive base)
    border?: number
    column?: number
    movement?: number // hover lift sensitivity
    transition?: number // animation duration
    ltr?: boolean
    ringed?: boolean
    offset?: number // text ring rotation offset
    blurOnRest?: boolean // NEW: only blur when not hovered
    className?: string
}

const defaultAvatars: Avatar[] = [
    { avatar: "https://i.pravatar.cc/150?u=a", name: "Garou" },
    { avatar: "https://i.pravatar.cc/150?u=b", name: "Johan" },
    { avatar: "https://i.pravatar.cc/150?u=c", name: "Light Yagami" },
    { avatar: "https://i.pravatar.cc/150?u=d", name: "Vegeta" },
    { avatar: "https://i.pravatar.cc/150?u=e", name: "Light Yagami" },
]

export function MaskedAvatars({
    avatars = defaultAvatars,
    size = 70,
    border = 8,
    column = 40,
    movement = 0.72,
    transition = 0.18,
    ringed = true,
    offset = -3,
    blurOnRest = true,
    className
}: MaskedAvatarsProps) {
    const [hoveredIndex, setHoveredIndex] = useState<number | null>(null)

    // Optimize derived values with useMemo
    const { dynamicSize, maskImage } = React.useMemo(() => {
        const dynamicSize = `clamp(${size - 20}px, ${size}px, ${size + 30}px)`
        const circle = (border * 2 + size) / 2
        const radX = circle - column - border
        const maskImage = `radial-gradient(${circle}px ${circle}px at ${radX}px 50%, transparent ${circle - 0.5}px, white ${circle}px)`
        return { dynamicSize, maskImage }
    }, [size, border, column])

    const transitionConfig = React.useMemo(() => ({
        type: "spring" as const,
        stiffness: 260,
        damping: 20,
    }), [])

    return (
        <div
            className={cn("relative flex items-center", className)}
            style={{
                gap: `min(6vw, ${size * 0.5}px)`,
                "--size": dynamicSize,
            } as React.CSSProperties}
            role="group"
            aria-label="Animated avatar group"
        >
            <div className="relative flex items-center">
                <ul
                    className="m-0 p-0 list-none grid grid-flow-col content-end"
                    style={{
                        height: column,
                        gridAutoColumns: column,
                        transform: `translateX(${(size - column) * 0.5}px)`,
                    }}
                    role="list"
                >
                    {avatars.map((person, index) => {
                        const isHovered = hoveredIndex === index
                        const isPrevHovered = hoveredIndex === index - 1

                        const baseOffset = -size * 1.5
                        const moveOffset = size * movement

                        const maskPosition = isPrevHovered
                            ? `0 ${baseOffset - moveOffset}px`
                            : isHovered
                                ? `0 ${baseOffset + moveOffset}px`
                                : `0 ${baseOffset}px`

                        return (
                            <motion.li
                                key={index}
                                className="relative grid content-end outline-none pointer-events-none will-change-transform"
                                role="listitem"
                                style={{
                                    width: dynamicSize,
                                    aspectRatio: "1/3",
                                    transform: `translate(
                    ${(size - column) * -0.5}px,
                    ${(size - column) * 0.5}px
                  )`,
                                    zIndex: avatars.length - index,
                                }}
                                tabIndex={0}
                                aria-label={`Avatar of ${person.name}`}
                                onMouseEnter={() => setHoveredIndex(index)}
                                onMouseLeave={() => setHoveredIndex(null)}
                                onFocus={() => setHoveredIndex(index)}
                                onBlur={() => setHoveredIndex(null)}
                                onTouchStart={() => setHoveredIndex(index)}
                            >
                                {ringed && (
                                    <div
                                        className="name absolute left-1/2 text-center uppercase font-mono font-normal pointer-events-none"
                                        aria-hidden="true"
                                        style={{
                                            width: size,
                                            height: size,
                                            borderRadius: "50%",
                                            bottom: 0,
                                            transform: `translate(-50%, ${isHovered ? -movement * 100 : 0
                                                }%)`,
                                            transition: `transform ${transition}s ease-out`,
                                        }}
                                    >
                                        {person.name.split("").map((char, i) => (
                                            <span
                                                key={i}
                                                className="absolute will-change-transform"
                                                style={{
                                                    offsetPath: "border-box",
                                                    offsetDistance: `${(offset + i) * 0.75}ch`,
                                                    offsetAnchor: "50% 130%",
                                                    transform: isHovered
                                                        ? "translate(0, 0)"
                                                        : "translate(0, 100%)",
                                                    filter: isHovered
                                                        ? "blur(0px)"
                                                        : blurOnRest
                                                            ? "blur(4px)"
                                                            : "blur(0px)",
                                                    opacity: isHovered ? 1 : 0,
                                                    transition: `transform ${transition}s ease-out, opacity ${transition}s ease-out, filter ${transition}s ease-out`,
                                                }}
                                            >
                                                {char}
                                            </span>
                                        ))}
                                    </div>
                                )}

                                <div className="avatar-holder absolute inset-0 grid content-end">
                                    <motion.span
                                        className={cn(
                                            "avatar inline-block w-full aspect-square rounded-full relative overflow-hidden pointer-events-auto border-[3px] border-background will-change-transform",
                                            "focus:ring-2 focus:ring-offset-2 focus:ring-foreground"
                                        )}
                                        role="img"
                                        aria-label={person.name}
                                        style={{
                                            maskImage: index === 0 ? "none" : maskImage,
                                            WebkitMaskImage: index === 0 ? "none" : maskImage,
                                            maskSize: "100% 400%",
                                            WebkitMaskSize: "100% 400%",
                                            maskRepeat: "no-repeat",
                                        }}
                                        animate={{
                                            maskPosition: index === 0 ? "0 0" : maskPosition,
                                            y: isHovered ? -movement * 100 + "%" : "0%",
                                            scale: isHovered ? 1.05 : 1,
                                            opacity:
                                                hoveredIndex !== null && hoveredIndex !== index
                                                    ? 0.7
                                                    : 1,
                                        }}
                                        transition={transitionConfig}
                                    >
                                        <img
                                            src={person.avatar}
                                            alt={person.name}
                                            className="absolute inset-0 w-full h-full object-cover bg-muted"
                                        />
                                    </motion.span>
                                </div>

                                {/* Hover/Tap Hit Area */}
                                <div className="absolute bottom-0 w-full aspect-square pointer-events-auto" />
                            </motion.li>
                        )
                    })}
                </ul>


            </div>
        </div>
    )
}

```

---

## `src\components\ui\menubar.tsx`

```tsx
"use client"

import * as React from "react"
import * as MenubarPrimitive from "@radix-ui/react-menubar"
import { CheckIcon, ChevronRightIcon, CircleIcon } from "lucide-react"

import { cn } from "@/lib/utils"

function Menubar({
  className,
  ...props
}: React.ComponentProps<typeof MenubarPrimitive.Root>) {
  return (
    <MenubarPrimitive.Root
      data-slot="menubar"
      className={cn(
        "bg-background flex h-9 items-center gap-1 rounded-md border p-1 shadow-xs",
        className
      )}
      {...props}
    />
  )
}

function MenubarMenu({
  ...props
}: React.ComponentProps<typeof MenubarPrimitive.Menu>) {
  return <MenubarPrimitive.Menu data-slot="menubar-menu" {...props} />
}

function MenubarGroup({
  ...props
}: React.ComponentProps<typeof MenubarPrimitive.Group>) {
  return <MenubarPrimitive.Group data-slot="menubar-group" {...props} />
}

function MenubarPortal({
  ...props
}: React.ComponentProps<typeof MenubarPrimitive.Portal>) {
  return <MenubarPrimitive.Portal data-slot="menubar-portal" {...props} />
}

function MenubarRadioGroup({
  ...props
}: React.ComponentProps<typeof MenubarPrimitive.RadioGroup>) {
  return (
    <MenubarPrimitive.RadioGroup data-slot="menubar-radio-group" {...props} />
  )
}

function MenubarTrigger({
  className,
  ...props
}: React.ComponentProps<typeof MenubarPrimitive.Trigger>) {
  return (
    <MenubarPrimitive.Trigger
      data-slot="menubar-trigger"
      className={cn(
        "focus:bg-accent focus:text-accent-foreground data-[state=open]:bg-accent data-[state=open]:text-accent-foreground flex items-center rounded-sm px-2 py-1 text-sm font-medium outline-hidden select-none",
        className
      )}
      {...props}
    />
  )
}

function MenubarContent({
  className,
  align = "start",
  alignOffset = -4,
  sideOffset = 8,
  ...props
}: React.ComponentProps<typeof MenubarPrimitive.Content>) {
  return (
    <MenubarPortal>
      <MenubarPrimitive.Content
        data-slot="menubar-content"
        align={align}
        alignOffset={alignOffset}
        sideOffset={sideOffset}
        className={cn(
          "bg-popover text-popover-foreground data-[state=open]:animate-in data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0 data-[state=closed]:zoom-out-95 data-[state=open]:zoom-in-95 data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-from-right-2 data-[side=right]:slide-in-from-left-2 data-[side=top]:slide-in-from-bottom-2 z-50 min-w-[12rem] origin-(--radix-menubar-content-transform-origin) overflow-hidden rounded-md border p-1 shadow-md",
          className
        )}
        {...props}
      />
    </MenubarPortal>
  )
}

function MenubarItem({
  className,
  inset,
  variant = "default",
  ...props
}: React.ComponentProps<typeof MenubarPrimitive.Item> & {
  inset?: boolean
  variant?: "default" | "destructive"
}) {
  return (
    <MenubarPrimitive.Item
      data-slot="menubar-item"
      data-inset={inset}
      data-variant={variant}
      className={cn(
        "focus:bg-accent focus:text-accent-foreground data-[variant=destructive]:text-destructive data-[variant=destructive]:focus:bg-destructive/10 dark:data-[variant=destructive]:focus:bg-destructive/20 data-[variant=destructive]:focus:text-destructive data-[variant=destructive]:*:[svg]:!text-destructive [&_svg:not([class*='text-'])]:text-muted-foreground relative flex cursor-default items-center gap-2 rounded-sm px-2 py-1.5 text-sm outline-hidden select-none data-[disabled]:pointer-events-none data-[disabled]:opacity-50 data-[inset]:pl-8 [&_svg]:pointer-events-none [&_svg]:shrink-0 [&_svg:not([class*='size-'])]:size-4",
        className
      )}
      {...props}
    />
  )
}

function MenubarCheckboxItem({
  className,
  children,
  checked,
  ...props
}: React.ComponentProps<typeof MenubarPrimitive.CheckboxItem>) {
  return (
    <MenubarPrimitive.CheckboxItem
      data-slot="menubar-checkbox-item"
      className={cn(
        "focus:bg-accent focus:text-accent-foreground relative flex cursor-default items-center gap-2 rounded-xs py-1.5 pr-2 pl-8 text-sm outline-hidden select-none data-[disabled]:pointer-events-none data-[disabled]:opacity-50 [&_svg]:pointer-events-none [&_svg]:shrink-0 [&_svg:not([class*='size-'])]:size-4",
        className
      )}
      checked={checked}
      {...props}
    >
      <span className="pointer-events-none absolute left-2 flex size-3.5 items-center justify-center">
        <MenubarPrimitive.ItemIndicator>
          <CheckIcon className="size-4" />
        </MenubarPrimitive.ItemIndicator>
      </span>
      {children}
    </MenubarPrimitive.CheckboxItem>
  )
}

function MenubarRadioItem({
  className,
  children,
  ...props
}: React.ComponentProps<typeof MenubarPrimitive.RadioItem>) {
  return (
    <MenubarPrimitive.RadioItem
      data-slot="menubar-radio-item"
      className={cn(
        "focus:bg-accent focus:text-accent-foreground relative flex cursor-default items-center gap-2 rounded-xs py-1.5 pr-2 pl-8 text-sm outline-hidden select-none data-[disabled]:pointer-events-none data-[disabled]:opacity-50 [&_svg]:pointer-events-none [&_svg]:shrink-0 [&_svg:not([class*='size-'])]:size-4",
        className
      )}
      {...props}
    >
      <span className="pointer-events-none absolute left-2 flex size-3.5 items-center justify-center">
        <MenubarPrimitive.ItemIndicator>
          <CircleIcon className="size-2 fill-current" />
        </MenubarPrimitive.ItemIndicator>
      </span>
      {children}
    </MenubarPrimitive.RadioItem>
  )
}

function MenubarLabel({
  className,
  inset,
  ...props
}: React.ComponentProps<typeof MenubarPrimitive.Label> & {
  inset?: boolean
}) {
  return (
    <MenubarPrimitive.Label
      data-slot="menubar-label"
      data-inset={inset}
      className={cn(
        "px-2 py-1.5 text-sm font-medium data-[inset]:pl-8",
        className
      )}
      {...props}
    />
  )
}

function MenubarSeparator({
  className,
  ...props
}: React.ComponentProps<typeof MenubarPrimitive.Separator>) {
  return (
    <MenubarPrimitive.Separator
      data-slot="menubar-separator"
      className={cn("bg-border -mx-1 my-1 h-px", className)}
      {...props}
    />
  )
}

function MenubarShortcut({
  className,
  ...props
}: React.ComponentProps<"span">) {
  return (
    <span
      data-slot="menubar-shortcut"
      className={cn(
        "text-muted-foreground ml-auto text-xs tracking-widest",
        className
      )}
      {...props}
    />
  )
}

function MenubarSub({
  ...props
}: React.ComponentProps<typeof MenubarPrimitive.Sub>) {
  return <MenubarPrimitive.Sub data-slot="menubar-sub" {...props} />
}

function MenubarSubTrigger({
  className,
  inset,
  children,
  ...props
}: React.ComponentProps<typeof MenubarPrimitive.SubTrigger> & {
  inset?: boolean
}) {
  return (
    <MenubarPrimitive.SubTrigger
      data-slot="menubar-sub-trigger"
      data-inset={inset}
      className={cn(
        "focus:bg-accent focus:text-accent-foreground data-[state=open]:bg-accent data-[state=open]:text-accent-foreground flex cursor-default items-center rounded-sm px-2 py-1.5 text-sm outline-none select-none data-[inset]:pl-8",
        className
      )}
      {...props}
    >
      {children}
      <ChevronRightIcon className="ml-auto h-4 w-4" />
    </MenubarPrimitive.SubTrigger>
  )
}

function MenubarSubContent({
  className,
  ...props
}: React.ComponentProps<typeof MenubarPrimitive.SubContent>) {
  return (
    <MenubarPrimitive.SubContent
      data-slot="menubar-sub-content"
      className={cn(
        "bg-popover text-popover-foreground data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0 data-[state=closed]:zoom-out-95 data-[state=open]:zoom-in-95 data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-from-right-2 data-[side=right]:slide-in-from-left-2 data-[side=top]:slide-in-from-bottom-2 z-50 min-w-[8rem] origin-(--radix-menubar-content-transform-origin) overflow-hidden rounded-md border p-1 shadow-lg",
        className
      )}
      {...props}
    />
  )
}

export {
  Menubar,
  MenubarPortal,
  MenubarMenu,
  MenubarTrigger,
  MenubarContent,
  MenubarGroup,
  MenubarSeparator,
  MenubarLabel,
  MenubarItem,
  MenubarShortcut,
  MenubarCheckboxItem,
  MenubarRadioGroup,
  MenubarRadioItem,
  MenubarSub,
  MenubarSubTrigger,
  MenubarSubContent,
}

```

---

## `src\components\ui\morph-text.tsx`

```tsx
"use client";

import React, { useId } from "react";
import { cn } from "@/lib/utils";

// ─── Types ─────────────────────────────────────────────────────────────────

export interface MorphTextProps {
  /**
   * Array of words / phrases to cycle through.
   * @default ["CREATE", "DESIGN", "DEVELOP"]
   */
  words?: string[];
  /**
   * Duration (ms) each word is displayed before transitioning.
   * @default 3000
   */
  interval?: number;
  /**
   * Optional subtext rendered beneath the morphing word.
   */
  subtext?: string;
  /**
   * Font size passed as a CSS value (e.g. "clamp(3rem, 15vw, 10rem)").
   * Defaults to a fluid clamp that scales with the viewport.
   */
  fontSize?: string;
  /**
   * Font family. Defaults to `"Space Grotesk", sans-serif`.
   */
  fontFamily?: string;
  /** Extra CSS classes on the root wrapper. */
  className?: string;
  /** Extra CSS classes on the morphing text container. */
  textClassName?: string;
  /** Extra CSS classes on the subtext element. */
  subtextClassName?: string;
}

// ─── Component ──────────────────────────────────────────────────────────────

export function MorphText({
  words = ["CREATE", "DESIGN", "DEVELOP"],
  interval = 3000,
  subtext,
  fontSize = "clamp(3rem, 15vw, 10rem)",
  fontFamily = '"Space Grotesk", sans-serif',
  className,
  textClassName,
  subtextClassName,
}: MorphTextProps) {
  // Unique ID so multiple instances don't share filter IDs
  const uid = useId().replace(/:/g, "");
  const filterId = `morph-threshold-${uid}`;

  const totalDuration = (interval / 1000) * words.length; // seconds
  const wordDuration = interval / 1000;

  // Build per-word keyframe + delay styles
  const wordStyles = words.map((_, i) => ({
    animationDelay: `${i * wordDuration}s`,
    animationDuration: `${totalDuration}s`,
  }));

  return (
    <div className={cn("morph-text-root relative flex flex-col items-center", className)}>
      {/* ── Threshold SVG filter (hidden) ─────────────────────────── */}
      <svg
        aria-hidden="true"
        focusable="false"
        style={{ position: "absolute", width: 0, height: 0, pointerEvents: "none" }}
      >
        <defs>
          <filter id={filterId}>
            <feColorMatrix
              in="SourceGraphic"
              type="matrix"
              values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 25 -9"
              result="goo"
            />
            <feComposite in="SourceGraphic" in2="goo" operator="atop" />
          </filter>
        </defs>
      </svg>

      {/* ── Morphing word container ────────────────────────────────── */}
      <div
        className={cn("morph-text-container relative select-none", textClassName)}
        style={{
          fontSize,
          fontWeight: 700,
          filter: `url(#${filterId})`,
          fontFamily,
        }}
      >
        {/* word rotator */}
        <div
          className="morph-word-rotator relative flex items-center justify-center"
          style={{ height: "1.2em", minWidth: "14ch" }}
        >
          {words.map((word, i) => (
            <span
              key={`${word}-${i}`}
              className="morph-word absolute"
              style={{
                top: "50%",
                left: "50%",
                transform: "translate(-50%, -50%)",
                opacity: 0,
                whiteSpace: "nowrap",
                animationName: "morph-word-rotate",
                animationTimingFunction: "ease-in-out",
                animationIterationCount: "infinite",
                animationFillMode: "both",
                ...wordStyles[i],
              }}
            >
              {word}
            </span>
          ))}
        </div>
      </div>

      {/* ── Optional subtext ──────────────────────────────────────── */}
      {subtext && (
        <p
          className={cn(
            "morph-subtext mt-8 uppercase tracking-[0.2em] text-[#888]",
            subtextClassName
          )}
          style={{
            fontSize: "1.2rem",
            opacity: 0,
            animation: "morph-fade-up 1s ease-out 1s forwards",
            fontFamily,
          }}
        >
          {subtext}
        </p>
      )}

      {/* ── Scoped keyframes ──────────────────────────────────────── */}
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@300;500;700&display=swap');

        @keyframes morph-word-rotate {
          0% {
            opacity: 0;
            filter: blur(20px);
            transform: translate(-50%, -50%) scale(0.8);
          }
          5% {
            opacity: 0.5;
            filter: blur(10px);
          }
          15%, 35% {
            opacity: 1;
            filter: blur(0px);
            transform: translate(-50%, -50%) scale(1);
          }
          45% {
            opacity: 0.5;
            filter: blur(10px);
          }
          50%, 100% {
            opacity: 0;
            filter: blur(20px);
            transform: translate(-50%, -50%) scale(1.2);
          }
        }

        @keyframes morph-fade-up {
          from { opacity: 0; transform: translateY(20px); }
          to   { opacity: 1; transform: translateY(0); }
        }
      `}</style>
    </div>
  );
}

export default MorphText;

```

---

## `src\components\ui\morphing-disclosure.tsx`

```tsx
import { cn } from "@/lib/utils"
import { AnimatePresence, motion } from "framer-motion"
import { Puzzle } from "lucide-react"
import * as React from 'react'
import { boolean } from "zod/v4"


interface MoriphingDisclosureItem {
    title: string,
    description?: string
}


interface MoriphingDisclosureProps {
    title: string,
    description?: string
    defaultOpen?: boolean,
    className?: string,
    icon?: React.ReactNode,
    id: any
}

function MoriphingDisclosure({
    title, description,
    className,
    defaultOpen = false,
    icon,
    id
}: MoriphingDisclosureProps) {

    const [open, setOpen] = React.useState(defaultOpen)
    return (



        <motion.div
            layout
            className={cn("details max-w-80 overflow-hidden border border-gray-500 px-4 py-2", className,
                open && 'ring-1 rounded-md my-2 transform-3d', id == 0 && 'rounded-t-md', id == 3 && 'rounded-b-md',
                
            )}
            transition={{
                type: "spring", stiffness: 500, damping: 40
            }}
        >

            <motion.div
                onClick={() => setOpen((prev) => !prev)}
                className="cursor-pointer flex justify-between py-1 items-center  "
            >

                <motion.h4>
                    {
                        icon ? icon : <Puzzle size={16} />
                    }


                </motion.h4>

                <p>{title}</p>

                <motion.span
                    animate={{ rotate: open ? 180 : 0 }}
                    transition={{ type: "spring", stiffness: 400, damping: 30 }}
                >
                    +
                </motion.span>

            </motion.div>

            <AnimatePresence initial={false} mode="popLayout">
                {
                    open && (
                        <motion.div
                            key='panel'
                            layout
                            initial={{ opacity: 0 }}
                            animate={{ opacity: 1 }}
                            exit={{ opacity: 0 }}

                        >
                            <motion.div
                                layout
                            >
                                <motion.p
                                    initial={{ opacity: 0, y: 15 }}
                                    animate={{ opacity: 1, y: 0 }}
                                    transition={{ duration: 0.8, delay: 0.6, ease: [0.23, 1, 0.32, 1] }}
                                    className="text-lg md:text-xl text-zinc-600 dark:text-zinc-400 max-w-2xl mx-auto leading-relaxed  font-light"
                                >
                                    {description}
                                </motion.p>
                            </motion.div>
                        </motion.div>
                    )
                }

            </AnimatePresence>



        </motion.div>



    )
}

export { MoriphingDisclosure }
```

---

## `src\components\ui\music-player.tsx`

```tsx
"use client";

import * as React from "react";
import { useCallback, useEffect, useRef, useState } from "react";
import { Minus, Pause, Play, Plus, SkipBack, SkipForward } from "lucide-react";
import { cn } from "@/lib/utils";

/**
 * Music Player
 *
 * A collapsible, glassmorphic music player: a floating avatar peeks out of the
 * top-left, an animated equalizer pulses while audio plays, and a slim seekable
 * progress bar tracks position. The whole bar collapses to a compact pill,
 * hiding the track info and transport controls behind a soft width transition.
 *
 * Ported from the vanilla "Azuki Style Music Player" (CodeGrid) into a single,
 * self-contained, prop-driven React component. The Lottie sound-bars and
 * Ionicons of the original are replaced with a pure-CSS equalizer and
 * `lucide-react` icons, so the only runtime dependency is the icon set.
 */

export interface MusicTrack {
  /** Track title shown in the player. */
  title: string;
  /** Artist / author name shown under the title. */
  artist: string;
  /** URL of the audio file. Must be same-origin or CORS-enabled to stream. */
  src: string;
  /** Optional per-track artwork; falls back to the player `avatar`. */
  artwork?: string;
}

export interface MusicPlayerProps {
  /** Playlist to play through. The player renders nothing when empty. */
  tracks: MusicTrack[];
  /** Floating avatar image. Falls back to the current track's `artwork`. */
  avatar?: string;
  /** Index of the track to start on. Defaults to 0. */
  startIndex?: number;
  /** Begin playing as soon as the player mounts. Defaults to false. */
  autoPlay?: boolean;
  /** Wrap from the last track back to the first when a track ends. Defaults to true. */
  loop?: boolean;
  /** Render collapsed (compact pill) on first paint. Defaults to false. */
  defaultCollapsed?: boolean;
  /** Show the seekable progress bar along the bottom edge. Defaults to true. */
  showProgress?: boolean;
  /** Accent color for the equalizer and progress fill. Defaults to `currentColor`. */
  accentColor?: string;
  /** Called whenever the active track changes, with the track and its index. */
  onTrackChange?: (track: MusicTrack, index: number) => void;
  /** Extra class names for the root element. */
  className?: string;
}

const EQ_BARS = [0, 1, 2, 3];

/** Shared equalizer keyframes — identical across instances, so safe to repeat. */
const EQ_KEYFRAMES =
  "@keyframes vengeance-eq{0%,100%{transform:scaleY(0.28)}50%{transform:scaleY(1)}}";

function formatTime(seconds: number): string {
  if (!Number.isFinite(seconds) || seconds < 0) return "0:00";
  const m = Math.floor(seconds / 60);
  const s = Math.floor(seconds % 60);
  return `${m}:${s.toString().padStart(2, "0")}`;
}

function clampIndex(index: number, length: number): number {
  if (length === 0) return 0;
  return Math.min(Math.max(index, 0), length - 1);
}

export function MusicPlayer({
  tracks,
  avatar,
  startIndex = 0,
  autoPlay = false,
  loop = true,
  defaultCollapsed = false,
  showProgress = true,
  accentColor,
  onTrackChange,
  className,
}: MusicPlayerProps) {
  const audioRef = useRef<HTMLAudioElement>(null);
  const [index, setIndex] = useState(() => clampIndex(startIndex, tracks.length));
  const [isPlaying, setIsPlaying] = useState(false);
  const [collapsed, setCollapsed] = useState(defaultCollapsed);
  const [currentTime, setCurrentTime] = useState(0);
  const [duration, setDuration] = useState(0);

  // Whether the next loaded track should auto-start (true after a manual skip
  // or a natural track-end, so playback continues seamlessly).
  const shouldPlayRef = useRef(autoPlay);
  // Keep the latest callback without re-running the load effect on every render.
  const onTrackChangeRef = useRef(onTrackChange);
  useEffect(() => {
    onTrackChangeRef.current = onTrackChange;
  }, [onTrackChange]);

  const track = tracks[index];

  // Load the current track and (if flagged) begin playing.
  useEffect(() => {
    const audio = audioRef.current;
    if (!audio || !track) return;

    audio.src = track.src;
    audio.load();
    setCurrentTime(0);
    onTrackChangeRef.current?.(track, index);

    if (shouldPlayRef.current) {
      audio.play().catch(() => {
        /* Autoplay can be blocked until the user interacts — ignore. */
      });
    }
    // Reload only when the source changes, not when the callback identity does.
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [index, track?.src]);

  const play = useCallback(() => {
    shouldPlayRef.current = true;
    audioRef.current?.play().catch(() => {});
  }, []);

  const pause = useCallback(() => {
    shouldPlayRef.current = false;
    audioRef.current?.pause();
  }, []);

  const togglePlay = useCallback(() => {
    if (isPlaying) pause();
    else play();
  }, [isPlaying, play, pause]);

  const next = useCallback(() => {
    if (tracks.length === 0) return;
    shouldPlayRef.current = true; // manual skips keep playing
    setIndex((i) => (i + 1) % tracks.length);
  }, [tracks.length]);

  const prev = useCallback(() => {
    if (tracks.length === 0) return;
    shouldPlayRef.current = true;
    setIndex((i) => (i - 1 + tracks.length) % tracks.length);
  }, [tracks.length]);

  const handleEnded = useCallback(() => {
    if (!loop && index === tracks.length - 1) {
      shouldPlayRef.current = false;
      setIsPlaying(false);
      return;
    }
    next();
  }, [loop, index, tracks.length, next]);

  const seek = useCallback(
    (event: React.MouseEvent<HTMLDivElement>) => {
      const audio = audioRef.current;
      if (!audio || !Number.isFinite(duration) || duration === 0) return;
      const rect = event.currentTarget.getBoundingClientRect();
      const ratio = (event.clientX - rect.left) / rect.width;
      audio.currentTime = Math.min(Math.max(ratio, 0), 1) * duration;
    },
    [duration],
  );

  if (tracks.length === 0 || !track) return null;

  const accent = accentColor ?? "currentColor";
  const artwork = avatar ?? track.artwork;
  const progress = duration > 0 ? (currentTime / duration) * 100 : 0;

  return (
    <div
      className={cn(
        "relative select-none text-white transition-[width] duration-700 ease-out",
        collapsed ? "w-[188px]" : "w-[min(420px,90vw)]",
        className,
      )}
    >
      <style>{EQ_KEYFRAMES}</style>

      <audio
        ref={audioRef}
        preload="metadata"
        onPlay={() => setIsPlaying(true)}
        onPause={() => setIsPlaying(false)}
        onTimeUpdate={(e) => setCurrentTime(e.currentTarget.currentTime)}
        onLoadedMetadata={(e) => setDuration(e.currentTarget.duration)}
        onEnded={handleEnded}
      />

      {/* Collapse / expand toggle */}
      <button
        type="button"
        onClick={() => setCollapsed((c) => !c)}
        aria-label={collapsed ? "Expand player" : "Collapse player"}
        aria-expanded={!collapsed}
        className="absolute -right-3 -top-3 z-20 flex h-9 w-9 items-center justify-center rounded-full border border-white/20 bg-white/15 backdrop-blur-md transition-transform hover:scale-105 active:scale-95"
      >
        {collapsed ? <Plus className="h-4 w-4" /> : <Minus className="h-4 w-4" />}
      </button>

      {/* Floating avatar */}
      {artwork ? (
        // eslint-disable-next-line @next/next/no-img-element
        <img
          src={artwork}
          alt={`${track.title} artwork`}
          className="absolute -top-5 left-0 z-20 h-20 w-20 rounded-xl object-cover shadow-lg shadow-black/30 ring-1 ring-white/20"
        />
      ) : null}

      {/* Player bar */}
      <div className="relative flex h-[70px] items-center gap-3 overflow-hidden rounded-xl border border-white/15 bg-white/15 pl-24 pr-5 backdrop-blur-md">
        {/* Equalizer */}
        <div className="flex h-8 shrink-0 items-end gap-[3px]" aria-hidden="true">
          {EQ_BARS.map((bar) => (
            <span
              key={bar}
              className="block w-[3px] rounded-full"
              style={{
                height: "100%",
                background: accent,
                transformOrigin: "bottom",
                animation: `vengeance-eq ${0.9 + bar * 0.18}s ease-in-out infinite`,
                animationPlayState: isPlaying ? "running" : "paused",
                transform: isPlaying ? undefined : "scaleY(0.28)",
              }}
            />
          ))}
        </div>

        {/* Track info + controls, hidden while collapsed */}
        <div
          className={cn(
            "flex min-w-0 flex-1 items-center gap-3 transition-opacity duration-300",
            collapsed ? "pointer-events-none opacity-0" : "opacity-100",
          )}
          style={{ transitionDelay: collapsed ? "0s" : "0.35s" }}
        >
          <div className="min-w-0 flex-1">
            <div className="truncate text-sm font-semibold uppercase tracking-wide">
              {track.title}
            </div>
            <div className="truncate text-[0.65rem] uppercase tracking-[0.2em] text-white/50">
              {track.artist}
            </div>
          </div>

          <div className="flex shrink-0 items-center gap-1">
            <button
              type="button"
              onClick={prev}
              aria-label="Previous track"
              className="rounded-full p-1.5 opacity-80 transition hover:bg-white/10 hover:opacity-100"
            >
              <SkipBack className="h-4 w-4 fill-current" />
            </button>
            <button
              type="button"
              onClick={togglePlay}
              aria-label={isPlaying ? "Pause" : "Play"}
              className="rounded-full p-1.5 opacity-90 transition hover:bg-white/10 hover:opacity-100"
            >
              {isPlaying ? (
                <Pause className="h-5 w-5 fill-current" />
              ) : (
                <Play className="h-5 w-5 fill-current" />
              )}
            </button>
            <button
              type="button"
              onClick={next}
              aria-label="Next track"
              className="rounded-full p-1.5 opacity-80 transition hover:bg-white/10 hover:opacity-100"
            >
              <SkipForward className="h-4 w-4 fill-current" />
            </button>
          </div>
        </div>

        {/* Seekable progress bar */}
        {showProgress && !collapsed ? (
          <div
            role="slider"
            aria-label="Seek"
            aria-valuemin={0}
            aria-valuemax={Math.round(duration) || 0}
            aria-valuenow={Math.round(currentTime)}
            aria-valuetext={`${formatTime(currentTime)} of ${formatTime(duration)}`}
            tabIndex={0}
            onClick={seek}
            onKeyDown={(e) => {
              const audio = audioRef.current;
              if (!audio || !duration) return;
              if (e.key === "ArrowRight") audio.currentTime = Math.min(duration, currentTime + 5);
              if (e.key === "ArrowLeft") audio.currentTime = Math.max(0, currentTime - 5);
            }}
            className="group absolute inset-x-0 bottom-0 flex h-3 cursor-pointer items-end"
          >
            <div className="relative h-[3px] w-full bg-white/15">
              <div
                className="absolute inset-y-0 left-0 transition-[width] duration-100 ease-linear"
                style={{ width: `${progress}%`, background: accent }}
              />
            </div>
          </div>
        ) : null}
      </div>
    </div>
  );
}

export default MusicPlayer;

```

---

## `src\components\ui\navigation-menu.tsx`

```tsx
import * as React from "react"
import * as NavigationMenuPrimitive from "@radix-ui/react-navigation-menu"
import { cva } from "class-variance-authority"
import { ChevronDownIcon } from "lucide-react"

import { cn } from "@/lib/utils"

function NavigationMenu({
  className,
  children,
  viewport = true,
  ...props
}: React.ComponentProps<typeof NavigationMenuPrimitive.Root> & {
  viewport?: boolean
}) {
  return (
    <NavigationMenuPrimitive.Root
      data-slot="navigation-menu"
      data-viewport={viewport}
      className={cn(
        "group/navigation-menu relative flex max-w-max flex-1 items-center justify-center",
        className
      )}
      {...props}
    >
      {children}
      {viewport && <NavigationMenuViewport />}
    </NavigationMenuPrimitive.Root>
  )
}

function NavigationMenuList({
  className,
  ...props
}: React.ComponentProps<typeof NavigationMenuPrimitive.List>) {
  return (
    <NavigationMenuPrimitive.List
      data-slot="navigation-menu-list"
      className={cn(
        "group flex flex-1 list-none items-center justify-center gap-1",
        className
      )}
      {...props}
    />
  )
}

function NavigationMenuItem({
  className,
  ...props
}: React.ComponentProps<typeof NavigationMenuPrimitive.Item>) {
  return (
    <NavigationMenuPrimitive.Item
      data-slot="navigation-menu-item"
      className={cn("relative", className)}
      {...props}
    />
  )
}

const navigationMenuTriggerStyle = cva(
  "group inline-flex h-9 w-max items-center justify-center rounded-md bg-background px-4 py-2 text-sm font-medium hover:bg-accent hover:text-accent-foreground focus:bg-accent focus:text-accent-foreground disabled:pointer-events-none disabled:opacity-50 data-[state=open]:hover:bg-accent data-[state=open]:text-accent-foreground data-[state=open]:focus:bg-accent data-[state=open]:bg-accent/50 focus-visible:ring-ring/50 outline-none transition-[color,box-shadow] focus-visible:ring-[3px] focus-visible:outline-1"
)

function NavigationMenuTrigger({
  className,
  children,
  ...props
}: React.ComponentProps<typeof NavigationMenuPrimitive.Trigger>) {
  return (
    <NavigationMenuPrimitive.Trigger
      data-slot="navigation-menu-trigger"
      className={cn(navigationMenuTriggerStyle(), "group", className)}
      {...props}
    >
      {children}{" "}
      <ChevronDownIcon
        className="relative top-[1px] ml-1 size-3 transition duration-300 group-data-[state=open]:rotate-180"
        aria-hidden="true"
      />
    </NavigationMenuPrimitive.Trigger>
  )
}

function NavigationMenuContent({
  className,
  ...props
}: React.ComponentProps<typeof NavigationMenuPrimitive.Content>) {
  return (
    <NavigationMenuPrimitive.Content
      data-slot="navigation-menu-content"
      className={cn(
        "data-[motion^=from-]:animate-in data-[motion^=to-]:animate-out data-[motion^=from-]:fade-in data-[motion^=to-]:fade-out data-[motion=from-end]:slide-in-from-right-52 data-[motion=from-start]:slide-in-from-left-52 data-[motion=to-end]:slide-out-to-right-52 data-[motion=to-start]:slide-out-to-left-52 top-0 left-0 w-full p-2 pr-2.5 md:absolute md:w-auto",
        "group-data-[viewport=false]/navigation-menu:bg-popover group-data-[viewport=false]/navigation-menu:text-popover-foreground group-data-[viewport=false]/navigation-menu:data-[state=open]:animate-in group-data-[viewport=false]/navigation-menu:data-[state=closed]:animate-out group-data-[viewport=false]/navigation-menu:data-[state=closed]:zoom-out-95 group-data-[viewport=false]/navigation-menu:data-[state=open]:zoom-in-95 group-data-[viewport=false]/navigation-menu:data-[state=open]:fade-in-0 group-data-[viewport=false]/navigation-menu:data-[state=closed]:fade-out-0 group-data-[viewport=false]/navigation-menu:top-full group-data-[viewport=false]/navigation-menu:mt-1.5 group-data-[viewport=false]/navigation-menu:overflow-hidden group-data-[viewport=false]/navigation-menu:rounded-md group-data-[viewport=false]/navigation-menu:border group-data-[viewport=false]/navigation-menu:shadow group-data-[viewport=false]/navigation-menu:duration-200 **:data-[slot=navigation-menu-link]:focus:ring-0 **:data-[slot=navigation-menu-link]:focus:outline-none",
        className
      )}
      {...props}
    />
  )
}

function NavigationMenuViewport({
  className,
  ...props
}: React.ComponentProps<typeof NavigationMenuPrimitive.Viewport>) {
  return (
    <div
      className={cn(
        "absolute top-full left-0 isolate z-50 flex justify-center"
      )}
    >
      <NavigationMenuPrimitive.Viewport
        data-slot="navigation-menu-viewport"
        className={cn(
          "origin-top-center bg-popover text-popover-foreground data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:zoom-out-95 data-[state=open]:zoom-in-90 relative mt-1.5 h-[var(--radix-navigation-menu-viewport-height)] w-full overflow-hidden rounded-md border shadow md:w-[var(--radix-navigation-menu-viewport-width)]",
          className
        )}
        {...props}
      />
    </div>
  )
}

function NavigationMenuLink({
  className,
  ...props
}: React.ComponentProps<typeof NavigationMenuPrimitive.Link>) {
  return (
    <NavigationMenuPrimitive.Link
      data-slot="navigation-menu-link"
      className={cn(
        "data-[active=true]:focus:bg-accent data-[active=true]:hover:bg-accent data-[active=true]:bg-accent/50 data-[active=true]:text-accent-foreground hover:bg-accent hover:text-accent-foreground focus:bg-accent focus:text-accent-foreground focus-visible:ring-ring/50 [&_svg:not([class*='text-'])]:text-muted-foreground flex flex-col gap-1 rounded-sm p-2 text-sm transition-all outline-none focus-visible:ring-[3px] focus-visible:outline-1 [&_svg:not([class*='size-'])]:size-4",
        className
      )}
      {...props}
    />
  )
}

function NavigationMenuIndicator({
  className,
  ...props
}: React.ComponentProps<typeof NavigationMenuPrimitive.Indicator>) {
  return (
    <NavigationMenuPrimitive.Indicator
      data-slot="navigation-menu-indicator"
      className={cn(
        "data-[state=visible]:animate-in data-[state=hidden]:animate-out data-[state=hidden]:fade-out data-[state=visible]:fade-in top-full z-[1] flex h-1.5 items-end justify-center overflow-hidden",
        className
      )}
      {...props}
    >
      <div className="bg-border relative top-[60%] h-2 w-2 rotate-45 rounded-tl-sm shadow-md" />
    </NavigationMenuPrimitive.Indicator>
  )
}

export {
  NavigationMenu,
  NavigationMenuList,
  NavigationMenuItem,
  NavigationMenuContent,
  NavigationMenuTrigger,
  NavigationMenuLink,
  NavigationMenuIndicator,
  NavigationMenuViewport,
  navigationMenuTriggerStyle,
}

```

---

## `src\components\ui\notch-navbar.tsx`

```tsx
"use client"
import { useState, useEffect } from "react"
import Link from "next/link"
import { ArrowUpRight, Home, User, Calendar, Zap, CreditCard, Menu, X, Sun, Moon } from "lucide-react"
import { cn } from "@/lib/utils"
import { ThemeToggle } from "@/components/theme-toggle"
import LogoIcon from '@/assets/logo/logo-icon'
import { motion, AnimatePresence } from "framer-motion"
import { useTheme } from "next-themes"

// Helper component for navigation links
const NavLink = ({ href, icon: Icon, label }: { href: string; icon: React.ComponentType<{ className?: string }>; label: string }) => (
  <Link 
    href={href} 
    className="group flex items-center gap-1.5 text-sm font-medium text-foreground/70 hover:text-foreground transition-colors whitespace-nowrap"
  >
    <Icon className="w-4 h-4 opacity-70 group-hover:opacity-100" />
    <span>{label}</span>
  </Link>
)

// Simple Theme Toggle for Mobile
const MobileThemeToggle = () => {
  const { theme, setTheme, resolvedTheme } = useTheme()
  const [mounted, setMounted] = useState(false)

  useEffect(() => setMounted(true), [])

  if (!mounted) return <div className="w-9 h-9" />

  const isDark = (theme === 'dark' || resolvedTheme === 'dark')

  return (
    <button
      onClick={() => setTheme(isDark ? 'light' : 'dark')}
      className="flex items-center justify-center w-9 h-9 rounded-full hover:bg-foreground/5 transition-colors text-foreground/70 hover:text-foreground"
      aria-label="Toggle theme"
    >
      {isDark ? <Sun className="w-5 h-5" /> : <Moon className="w-5 h-5" />}
    </button>
  )
}

export function NotchNavbar({ className, ...props }: React.HTMLAttributes<HTMLElement> & { logo?: React.ReactNode }) {
  const [isMobileMenuOpen, setIsMobileMenuOpen] = useState(false)

  // Navigation items configuration
  const items = {
    left: [
      { label: "Home", href: "#home", icon: Home },
      { label: "About", href: "#about", icon: User },
      { label: "Events", href: "#events", icon: Calendar }
    ],
    right: [
      { label: "Sponsors", href: "#sponsors", icon: Zap },
      { label: "Pricing", href: "#pricing", icon: CreditCard }
    ]
  }

  return (
    <>
      <header className={cn("fixed top-0 inset-x-0 z-50 h-16 flex px-0", className)} {...props}>
        
        {/* Left Side Bar - Flexible width */}
        <div className="flex-1 h-10 bg-zinc-50 dark:bg-black z-20 relative min-w-0">
          <svg className="absolute inset-0 w-full h-full" preserveAspectRatio="none">
            <line x1="0" y1="39.5" x2="100%" y2="39.5" stroke="currentColor" strokeOpacity={0.05} strokeWidth={0.5} className="text-foreground" />
            <line x1="0" y1="36.5" x2="100%" y2="36.5" stroke="currentColor" strokeOpacity={0.05} strokeWidth={0.5} className="text-foreground" />
          </svg>
        </div>

        {/* Responsive Notch Container - 3 Slices */}
        <div className="flex h-16 relative z-10 shrink-0 -ml-px">
          
          {/* Left Slice (Corner) */}
          <div className="w-[50px] h-full relative shrink-0">
            {/* Glass Background */}
            <div className="absolute inset-0 bg-zinc-50 dark:bg-black" style={{ clipPath: "path('M0 0 H50 V64 C25 64 25 40 0 40 Z')" }} />
            {/* Outlines */}
            <svg className="absolute inset-0 w-full h-full pointer-events-none" viewBox="0 0 50 64">
              <path d="M0 39.5 C25 39.5 25 63.5 50 63.5" fill="none" stroke="currentColor" strokeOpacity={0.05} strokeWidth={0.5} className="text-foreground" />
              <path d="M0 36.5 C25 36.5 25 60.5 50 60.5" fill="none" stroke="currentColor" strokeOpacity={0.05} strokeWidth={0.5} className="text-foreground" />
            </svg>
          </div>

          {/* Center Slice (Flexible Content Area) */}
          <div className="flex-1 h-full relative min-w-0 -ml-px">
             {/* Background & Lines Layer */}
             <div className="absolute inset-0 bg-zinc-50 dark:bg-black">
                 <svg className="absolute inset-0 w-full h-full pointer-events-none" preserveAspectRatio="none">
                   <line x1="0" y1="63.5" x2="100%" y2="63.5" stroke="currentColor" strokeOpacity={0.05} strokeWidth={0.5} className="text-foreground" />
                   <line x1="0" y1="60.5" x2="100%" y2="60.5" stroke="currentColor" strokeOpacity={0.05} strokeWidth={0.5} className="text-foreground" />
                 </svg>
             </div>

             {/* Content Layer */}
             <div className="relative w-full h-full flex items-end justify-between pb-2 px-4 md:px-8">
               
               {/* Desktop Left Nav */}
               <nav className="hidden md:flex gap-8 mb-1 shrink-0">
                {items.left.map(item => (
                  <NavLink key={item.label} {...item} />
                ))}
              </nav>

              {/* Mobile Menu Button (Left) */}
              <button 
                className="md:hidden mb-1 p-1 text-foreground/70 hover:text-foreground transition-colors"
                onClick={() => setIsMobileMenuOpen(!isMobileMenuOpen)}
                aria-label="Toggle menu"
              >
                {isMobileMenuOpen ? <X className="w-5 h-5" /> : <Menu className="w-5 h-5" />}
              </button>

              {/* Logo (Center) */}
              <div className="flex justify-center shrink-0 mx-2 md:mx-4 mt-1">
                {props.logo || (
                  <Link href="/" className="flex items-center justify-center relative group">
                    <LogoIcon className="w-7 h-7 text-foreground rotate-180 hover:scale-105 transition-transform relative z-10" />
                  </Link>
                )}
              </div>

              {/* Desktop Right Nav */}
              <nav className="hidden md:flex gap-6 items-center shrink-0">
                {items.right.map(item => (
                  <NavLink key={item.label} {...item} />
                ))}
                
                <div className="flex gap-4 pl-4 border-l border-foreground/10 shrink-0 items-center">
                  <ThemeToggle />
                  <Link href="/login" className="text-sm font-medium text-foreground/70 hover:text-foreground transition-colors whitespace-nowrap">
                    Log in
                  </Link>
                  <Link href="/signup" className="px-3 py-1.5 text-sm font-medium text-background bg-foreground rounded-2xl hover:bg-foreground/90 transition-colors shadow-sm shadow-foreground/10 whitespace-nowrap">
                    Sign up
                  </Link>
                </div>
              </nav>

              {/* Mobile Right Actions */}
              <div className="md:hidden flex items-center gap-2 mb-1">
                <MobileThemeToggle />
              </div>

             </div>
          </div>

          {/* Right Slice (Corner) */}
          <div className="w-[50px] h-full relative shrink-0 -ml-px">
            {/* Glass Background */}
            <div className="absolute inset-0 bg-zinc-50 dark:bg-black" style={{ clipPath: "path('M0 0 H50 V40 C25 40 25 64 0 64 Z')" }} />
            {/* Outlines */}
            <svg className="absolute inset-0 w-full h-full pointer-events-none" viewBox="0 0 50 64">
              <path d="M0 63.5 C25 63.5 25 39.5 50 39.5" fill="none" stroke="currentColor" strokeOpacity={0.05} strokeWidth={0.5} className="text-foreground" />
              <path d="M0 60.5 C25 60.5 25 36.5 50 36.5" fill="none" stroke="currentColor" strokeOpacity={0.05} strokeWidth={0.5} className="text-foreground" />
            </svg>
          </div>

        </div>

        {/* Right Side Bar - Flexible width */}
        <div className="flex-1 h-10 bg-zinc-50 dark:bg-black z-20 relative min-w-0 -ml-px">
          <svg className="absolute inset-0 w-full h-full" preserveAspectRatio="none">
            <line x1="0" y1="39.5" x2="100%" y2="39.5" stroke="currentColor" strokeOpacity={0.05} strokeWidth={0.5} className="text-foreground" />
            <line x1="0" y1="36.5" x2="100%" y2="36.5" stroke="currentColor" strokeOpacity={0.05} strokeWidth={0.5} className="text-foreground" />
          </svg>
        </div>

      </header>

      {/* Mobile Menu Overlay */}
      <AnimatePresence>
        {isMobileMenuOpen && (
          <motion.div
            initial={{ opacity: 0, y: -20 }}
            animate={{ opacity: 1, y: 0 }}
            exit={{ opacity: 0, y: -20 }}
            transition={{ duration: 0.2 }}
            className="fixed inset-x-0 top-16 z-40 bg-zinc-50 dark:bg-black border-b border-foreground/5 p-4 md:hidden shadow-lg"
          >
             <nav className="flex flex-col gap-2">
               {/* Combine all items */}
               {[...items.left, ...items.right].map(item => (
                 <Link 
                   key={item.label} 
                   href={item.href}
                   className="flex items-center gap-3 p-3 rounded-lg hover:bg-foreground/5 transition-colors"
                   onClick={() => setIsMobileMenuOpen(false)}
                 >
                   <item.icon className="w-5 h-5 opacity-70" />
                   <span className="font-medium text-foreground/90">{item.label}</span>
                 </Link>
               ))}
               <div className="h-px bg-foreground/10 my-2" />
               <div className="flex flex-col gap-2">
                 <Link 
                    href="/login" 
                    className="flex items-center gap-3 p-3 rounded-lg hover:bg-foreground/5 transition-colors font-medium text-foreground/90"
                    onClick={() => setIsMobileMenuOpen(false)}
                 >
                   Log in
                 </Link>
                 <Link 
                    href="/signup" 
                    className="flex items-center justify-center gap-2 p-3 rounded-lg bg-foreground text-background font-medium mt-2"
                    onClick={() => setIsMobileMenuOpen(false)}
                 >
                   Sign up
                 </Link>
               </div>
             </nav>

          </motion.div>
        )}
      </AnimatePresence>
    </>
  )
}

```

---

## `src\components\ui\pagination.tsx`

```tsx
import * as React from "react"
import {
  ChevronLeftIcon,
  ChevronRightIcon,
  MoreHorizontalIcon,
} from "lucide-react"

import { cn } from "@/lib/utils"
import { Button, buttonVariants } from "@/components/ui/button"

function Pagination({ className, ...props }: React.ComponentProps<"nav">) {
  return (
    <nav
      role="navigation"
      aria-label="pagination"
      data-slot="pagination"
      className={cn("mx-auto flex w-full justify-center", className)}
      {...props}
    />
  )
}

function PaginationContent({
  className,
  ...props
}: React.ComponentProps<"ul">) {
  return (
    <ul
      data-slot="pagination-content"
      className={cn("flex flex-row items-center gap-1", className)}
      {...props}
    />
  )
}

function PaginationItem({ ...props }: React.ComponentProps<"li">) {
  return <li data-slot="pagination-item" {...props} />
}

type PaginationLinkProps = {
  isActive?: boolean
} & Pick<React.ComponentProps<typeof Button>, "size"> &
  React.ComponentProps<"a">

function PaginationLink({
  className,
  isActive,
  size = "icon",
  ...props
}: PaginationLinkProps) {
  return (
    <a
      aria-current={isActive ? "page" : undefined}
      data-slot="pagination-link"
      data-active={isActive}
      className={cn(
        buttonVariants({
          variant: isActive ? "outline" : "ghost",
          size,
        }),
        className
      )}
      {...props}
    />
  )
}

function PaginationPrevious({
  className,
  ...props
}: React.ComponentProps<typeof PaginationLink>) {
  return (
    <PaginationLink
      aria-label="Go to previous page"
      size="default"
      className={cn("gap-1 px-2.5 sm:pl-2.5", className)}
      {...props}
    >
      <ChevronLeftIcon />
      <span className="hidden sm:block">Previous</span>
    </PaginationLink>
  )
}

function PaginationNext({
  className,
  ...props
}: React.ComponentProps<typeof PaginationLink>) {
  return (
    <PaginationLink
      aria-label="Go to next page"
      size="default"
      className={cn("gap-1 px-2.5 sm:pr-2.5", className)}
      {...props}
    >
      <span className="hidden sm:block">Next</span>
      <ChevronRightIcon />
    </PaginationLink>
  )
}

function PaginationEllipsis({
  className,
  ...props
}: React.ComponentProps<"span">) {
  return (
    <span
      aria-hidden
      data-slot="pagination-ellipsis"
      className={cn("flex size-9 items-center justify-center", className)}
      {...props}
    >
      <MoreHorizontalIcon className="size-4" />
      <span className="sr-only">More pages</span>
    </span>
  )
}

export {
  Pagination,
  PaginationContent,
  PaginationLink,
  PaginationItem,
  PaginationPrevious,
  PaginationNext,
  PaginationEllipsis,
}

```

---

## `src\components\ui\perspective-carousel.tsx`

```tsx
"use client";

import * as React from "react";
import { motion, type Transition } from "framer-motion";
import { ChevronLeft, ChevronRight } from "lucide-react";
import { cn } from "@/lib/utils";

export interface PerspectiveCarouselItem {
  src: string;
  title: string;
  alt?: string;
}

export interface PerspectiveCarouselProps
  extends Omit<React.HTMLAttributes<HTMLDivElement>, "onChange"> {
  items: PerspectiveCarouselItem[];
  activeIndex?: number;
  defaultActiveIndex?: number;
  onActiveIndexChange?: (index: number) => void;
  loop?: boolean;
  slideWidth?: number;
  rotationStep?: number;
  inactiveScale?: number;
  transition?: Transition;
  showControls?: boolean;
  showDots?: boolean;
  viewportClassName?: string;
  slideClassName?: string;
  imageClassName?: string;
  labelClassName?: string;
  controlsClassName?: string;
}

const DEFAULT_TRANSITION: Transition = {
  type: "spring",
  bounce: 0.14,
  duration: 0.9,
};

const clamp = (value: number, min: number, max: number) => Math.min(Math.max(value, min), max);

export function PerspectiveCarousel({
  items,
  activeIndex,
  defaultActiveIndex = 0,
  onActiveIndexChange,
  loop = false,
  slideWidth = 200,
  rotationStep = 60,
  inactiveScale = 0.85,
  transition = DEFAULT_TRANSITION,
  showControls = true,
  showDots = true,
  viewportClassName,
  slideClassName,
  imageClassName,
  labelClassName,
  controlsClassName,
  className,
  onKeyDown,
  tabIndex,
  ...props
}: PerspectiveCarouselProps) {
  const maxIndex = Math.max(0, items.length - 1);
  const [uncontrolledIndex, setUncontrolledIndex] = React.useState(() =>
    clamp(defaultActiveIndex, 0, maxIndex)
  );
  const currentIndex = clamp(activeIndex ?? uncontrolledIndex, 0, maxIndex);
  const safeSlideWidth = Math.max(96, slideWidth);
  const safeInactiveScale = clamp(inactiveScale, 0.5, 1);

  const selectSlide = React.useCallback(
    (nextIndex: number) => {
      if (!items.length) {
        return;
      }

      const resolvedIndex = loop
        ? (nextIndex + items.length) % items.length
        : clamp(nextIndex, 0, maxIndex);

      if (activeIndex === undefined) {
        setUncontrolledIndex(resolvedIndex);
      }

      onActiveIndexChange?.(resolvedIndex);
    },
    [activeIndex, items.length, loop, maxIndex, onActiveIndexChange]
  );

  if (!items.length) {
    return null;
  }

  const isPreviousDisabled = !loop && currentIndex === 0;
  const isNextDisabled = !loop && currentIndex === maxIndex;
  const handleKeyDown = (event: React.KeyboardEvent<HTMLDivElement>) => {
    onKeyDown?.(event);

    if (event.defaultPrevented) {
      return;
    }

    if (event.key === "ArrowLeft") {
      event.preventDefault();
      selectSlide(currentIndex - 1);
    }

    if (event.key === "ArrowRight") {
      event.preventDefault();
      selectSlide(currentIndex + 1);
    }
  };

  return (
    <div
      role="region"
      aria-roledescription="carousel"
      aria-label="Perspective image carousel"
      tabIndex={tabIndex ?? 0}
      onKeyDown={handleKeyDown}
      className={cn("relative isolate h-full w-full overflow-hidden", className)}
      {...props}
    >
      <div
        className={cn("absolute inset-0 overflow-hidden", viewportClassName)}
        style={{ perspective: "1200px" }}
      >
        <motion.div
          className="absolute left-1/2 top-1/2 flex w-fit -translate-y-1/2 items-center"
          animate={{ x: -(currentIndex * safeSlideWidth + safeSlideWidth / 2) }}
          transition={transition}
        >
          {items.map((item, index) => {
            const isActive = currentIndex === index;

            return (
              <div
                key={`${item.src}-${index}`}
                className="shrink-0"
                style={{ width: safeSlideWidth, perspective: "1200px" }}
              >
                <motion.div
                  className={cn(
                    "flex w-full flex-col items-center gap-3 will-change-transform",
                    slideClassName
                  )}
                  animate={{
                    rotateY: (currentIndex - index) * rotationStep,
                    scale: isActive ? 1 : safeInactiveScale,
                  }}
                  transition={transition}
                  style={{ transformStyle: "preserve-3d" }}
                >
                  <button
                    type="button"
                    aria-label={`Show ${item.title}`}
                    aria-current={isActive ? "true" : undefined}
                    className="aspect-[3/4] w-full cursor-pointer"
                    onClick={() => selectSlide(index)}
                  >
                    {/* eslint-disable-next-line @next/next/no-img-element */}
                    <img
                      src={item.src}
                      alt={item.alt ?? item.title}
                      draggable={false}
                      className={cn(
                        "h-full w-full select-none rounded-lg object-cover shadow-xl",
                        imageClassName
                      )}
                    />
                  </button>

                  <motion.p
                    className={cn("whitespace-nowrap text-sm", labelClassName)}
                    animate={{
                      filter: isActive ? "blur(0px)" : "blur(2px)",
                      opacity: isActive ? 1 : 0,
                    }}
                    transition={transition}
                  >
                    {item.title}
                  </motion.p>
                </motion.div>
              </div>
            );
          })}
        </motion.div>
      </div>

      {showControls && (
        <div
          className={cn(
            "absolute inset-x-4 bottom-5 z-10 mx-auto flex w-fit items-center justify-center gap-3 rounded-full border border-neutral-300/80 bg-neutral-200/70 px-2 text-neutral-700 shadow-sm backdrop-blur-sm dark:border-white/10 dark:bg-neutral-900/70 dark:text-neutral-100",
            controlsClassName
          )}
        >
          <button
            type="button"
            aria-label="Show previous slide"
            disabled={isPreviousDisabled}
            className="inline-flex size-9 items-center justify-center rounded-full transition-colors hover:bg-white/70 disabled:cursor-not-allowed disabled:opacity-35 dark:hover:bg-white/10"
            onClick={() => selectSlide(currentIndex - 1)}
          >
            <ChevronLeft className="size-5" />
          </button>

          {showDots && (
            <div className="flex items-center justify-center gap-2">
              {items.map((item, index) => (
                <button
                  key={`${item.title}-${index}`}
                  type="button"
                  aria-label={`Show slide ${index + 1}: ${item.title}`}
                  aria-current={currentIndex === index ? "true" : undefined}
                  className={cn(
                    "h-2 rounded-full bg-current transition-[width,opacity] duration-300",
                    currentIndex === index ? "w-7 opacity-100" : "w-2 opacity-30"
                  )}
                  onClick={() => selectSlide(index)}
                />
              ))}
            </div>
          )}

          <button
            type="button"
            aria-label="Show next slide"
            disabled={isNextDisabled}
            className="inline-flex size-9 items-center justify-center rounded-full transition-colors hover:bg-white/70 disabled:cursor-not-allowed disabled:opacity-35 dark:hover:bg-white/10"
            onClick={() => selectSlide(currentIndex + 1)}
          >
            <ChevronRight className="size-5" />
          </button>
        </div>
      )}
    </div>
  );
}

export default PerspectiveCarousel;

```

---

## `src\components\ui\perspective-grid.tsx`

```tsx
"use client";

import React, { useEffect, useState, useMemo } from "react";
import { cn } from "@/lib/utils";

interface PerspectiveGridProps {
    /** Additional CSS classes for the grid container */
    className?: string;
    /** Number of tiles per row/column (default: 40) */
    gridSize?: number;
    /** Whether to show the gradient overlay (default: true) */
    showOverlay?: boolean;
    /** Fade radius percentage for the gradient overlay (default: 80) */
    fadeRadius?: number;
}


export function PerspectiveGrid({
    className,
    gridSize = 40,
    showOverlay = true,
    fadeRadius = 80,
}: PerspectiveGridProps) {
    const [mounted, setMounted] = useState(false);

    useEffect(() => {
        setMounted(true);
    }, []);

    // Memoize tiles array to prevent unnecessary re-renders
    const tiles = useMemo(() => Array.from({ length: gridSize * gridSize }), [gridSize]);

    return (
        <div
            className={cn(
                "relative w-full h-full overflow-hidden bg-white dark:bg-black",
                "[--fade-stop:#ffffff] dark:[--fade-stop:#000000]",
                className
            )}
            style={{
                perspective: "2000px",
                transformStyle: "preserve-3d",
            }}
        >
            <div
                className="absolute w-[80rem] aspect-square grid origin-center"
                style={{
                    left: "50%",
                    top: "50%",
                    transform:
                        "translate(-50%, -50%) rotateX(30deg) rotateY(-5deg) rotateZ(20deg) scale(2)",
                    transformStyle: "preserve-3d",
                    gridTemplateColumns: `repeat(${gridSize}, 1fr)`,
                    gridTemplateRows: `repeat(${gridSize}, 1fr)`,
                }}
            >
                {/* Tiles */}
                {mounted &&
                    tiles.map((_, i) => (
                        <div
                            key={i}
                            className="tile min-h-[1px] min-w-[1px] border border-gray-300 dark:border-gray-700 bg-transparent transition-colors duration-[1500ms] hover:duration-0"
                        />
                    ))}
            </div>

            {/* Radial Gradient Mask (Overlay) */}
            {showOverlay && (
                <div
                    className="absolute inset-0 pointer-events-none z-10"
                    style={{
                        background: `radial-gradient(circle, transparent 25%, var(--fade-stop) ${fadeRadius}%)`,
                    }}
                />
            )}
        </div>
    );
}

export default PerspectiveGrid;

```

---

## `src\components\ui\pixelated-image-trail.tsx`

```tsx
"use client";

import { useEffect, useMemo, useRef } from "react";
import { cn } from "@/lib/utils";

interface TrailConfig {
  imageLifespan: number;
  inDuration: number;
  outDuration: number;
  staggerIn: number;
  staggerOut: number;
  slideDuration: number;
  slideEasing: string;
  easing: string;
}

export interface PixelatedImageTrailProps {
  className?: string;
  images?: string[];
  config?: Partial<TrailConfig>;
  slices?: number;
  spawnThreshold?: number;
  smoothing?: number;
  imageSize?: number;
}

const DEFAULT_CONFIG: TrailConfig = {
  imageLifespan: 1500,
  inDuration: 280,
  outDuration: 620,
  staggerIn: 12,
  staggerOut: 9,
  slideDuration: 1300,
  slideEasing: "cubic-bezier(0.16, 1, 0.3, 1)",
  easing: "cubic-bezier(0.16, 1, 0.3, 1)",
};

const DEFAULT_IMAGES = ["/trail-images/image1.jpg", "/trail-images/image4.jpg", "/trail-images/image5.jpg"];
const MAX_ACTIVE_IMAGES = 14;

const clamp = (value: number, min: number, max: number) => Math.min(Math.max(value, min), max);

export function PixelatedImageTrail({
  className,
  images,
  config: configOverride = {},
  slices = 5,
  spawnThreshold = 32,
  smoothing = 0.32,
  imageSize = 220,
}: PixelatedImageTrailProps) {
  const containerRef = useRef<HTMLDivElement>(null);
  const animationFrameRef = useRef<number | null>(null);
  const currentImageIndexRef = useRef(0);
  const validImagesRef = useRef<string[]>([]);
  const timeoutsRef = useRef<number[]>([]);
  const activeImagesRef = useRef<HTMLDivElement[]>([]);
  const pointerActiveRef = useRef(false);
  const pointerPosRef = useRef({ x: 0, y: 0 });
  const lastSpawnPosRef = useRef({ x: 0, y: 0 });
  const interpolatedPointerPosRef = useRef({ x: 0, y: 0 });

  const finalImages = useMemo(() => (images?.length ? images : DEFAULT_IMAGES), [images]);
  const finalImagesKey = finalImages.join("|");
  const config = useMemo(() => ({ ...DEFAULT_CONFIG, ...configOverride }), [configOverride]);

  useEffect(() => {
    let isActive = true;
    validImagesRef.current = [];

    finalImages.forEach((src) => {
      const image = new Image();

      image.onload = () => {
        if (!isActive || validImagesRef.current.includes(src)) {
          return;
        }

        validImagesRef.current.push(src);
      };

      image.src = src;
    });

    return () => {
      isActive = false;
    };
  }, [finalImagesKey, finalImages]);

  useEffect(() => {
    const container = containerRef.current;
    if (!container) return;

    const safeSlices = Math.max(1, Math.floor(slices));
    const safeSmoothing = clamp(smoothing, 0.01, 1);
    const safeSpawnThreshold = Math.max(1, spawnThreshold);
    const safeImageSize = Math.max(40, imageSize);
    const getSliceDelay = (index: number, stagger: number) =>
      Math.abs(index - (safeSlices - 1) / 2) * stagger;
    const getMaxSliceDelay = (stagger: number) => ((safeSlices - 1) / 2) * stagger;

    const schedule = (callback: () => void, delay: number) => {
      const timeout = window.setTimeout(() => {
        timeoutsRef.current = timeoutsRef.current.filter((id) => id !== timeout);
        callback();
      }, delay);

      timeoutsRef.current.push(timeout);
      return timeout;
    };

    const updatePointer = (event: PointerEvent) => {
      const rect = container.getBoundingClientRect();
      const nextPosition = {
        x: event.clientX - rect.left,
        y: event.clientY - rect.top,
      };

      pointerPosRef.current = nextPosition;

      if (!pointerActiveRef.current) {
        pointerActiveRef.current = true;
        interpolatedPointerPosRef.current = nextPosition;
        lastSpawnPosRef.current = nextPosition;
      }
    };

    const handlePointerLeave = () => {
      pointerActiveRef.current = false;
    };

    const distanceFromLastSpawn = () => {
      const dx = interpolatedPointerPosRef.current.x - lastSpawnPosRef.current.x;
      const dy = interpolatedPointerPosRef.current.y - lastSpawnPosRef.current.y;

      return Math.hypot(dx, dy);
    };

    const createTrailImage = () => {
      if (!validImagesRef.current.length) return;

      const imageSource = validImagesRef.current[currentImageIndexRef.current % validImagesRef.current.length];
      currentImageIndexRef.current = (currentImageIndexRef.current + 1) % validImagesRef.current.length;

      const startX = interpolatedPointerPosRef.current.x - safeImageSize / 2;
      const startY = interpolatedPointerPosRef.current.y - safeImageSize / 2;
      const targetX = startX + (pointerPosRef.current.x - interpolatedPointerPosRef.current.x) * 0.45;
      const targetY = startY + (pointerPosRef.current.y - interpolatedPointerPosRef.current.y) * 0.45;
      const imageElement = document.createElement("div");
      const layerFragment = document.createDocumentFragment();

      Object.assign(imageElement.style, {
        position: "absolute",
        left: `${startX}px`,
        top: `${startY}px`,
        width: `${safeImageSize}px`,
        height: `${safeImageSize}px`,
        pointerEvents: "none",
        overflow: "hidden",
        borderRadius: "12px",
        opacity: "1",
        transform: "translate3d(0, 0, 0) scale(1)",
        transition: [
          `left ${config.slideDuration}ms ${config.slideEasing}`,
          `top ${config.slideDuration}ms ${config.slideEasing}`,
          `opacity ${config.outDuration}ms ${config.easing}`,
          `transform ${config.outDuration}ms ${config.easing}`,
        ].join(", "),
        willChange: "left, top, opacity, transform",
        zIndex: "1",
        filter: "drop-shadow(0 16px 24px rgb(0 0 0 / 0.22))",
        contain: "layout style paint",
        backfaceVisibility: "hidden",
      });

      const layers: HTMLDivElement[] = [];

      for (let index = 0; index < safeSlices; index += 1) {
        const sliceSize = 100 / safeSlices;
        const startClipY = index * sliceSize;
        const endClipY = (index + 1) * sliceSize;
        const layer = document.createElement("div");
        const imageLayer = document.createElement("div");

        Object.assign(layer.style, {
          position: "absolute",
          inset: "0",
          overflow: "hidden",
          clipPath: `polygon(50% ${startClipY}%, 50% ${startClipY}%, 50% ${endClipY}%, 50% ${endClipY}%)`,
          transition: `clip-path ${config.inDuration}ms ${config.easing}`,
          transitionDelay: `${getSliceDelay(index, config.staggerIn)}ms`,
          transform: "translateZ(0)",
          backfaceVisibility: "hidden",
          willChange: "clip-path",
          contain: "layout style",
        });

        Object.assign(imageLayer.style, {
          position: "absolute",
          inset: "0",
          backgroundImage: `url("${imageSource}")`,
          backgroundSize: "cover",
          backgroundPosition: "center",
          borderRadius: "12px",
          transform: "translateZ(0)",
          backfaceVisibility: "hidden",
          boxShadow: "inset 0 0 0 1px rgb(255 255 255 / 0.08)",
        });

        layer.appendChild(imageLayer);
        layerFragment.appendChild(layer);
        layers.push(layer);
      }

      imageElement.appendChild(layerFragment);
      container.appendChild(imageElement);
      activeImagesRef.current.push(imageElement);

      while (activeImagesRef.current.length > MAX_ACTIVE_IMAGES) {
        activeImagesRef.current.shift()?.remove();
      }

      requestAnimationFrame(() => {
        if (imageElement.parentElement !== container) return;

        imageElement.style.left = `${targetX}px`;
        imageElement.style.top = `${targetY}px`;

        layers.forEach((layer, index) => {
          const sliceSize = 100 / safeSlices;
          const startClipY = index * sliceSize;
          const endClipY = (index + 1) * sliceSize;

          layer.style.clipPath = `polygon(0% ${startClipY}%, 100% ${startClipY}%, 100% ${endClipY}%, 0% ${endClipY}%)`;
        });
      });

      schedule(() => {
        imageElement.style.opacity = "0";
        imageElement.style.transform = "translate3d(0, 0, 0) scale(0.24)";

        layers.forEach((layer, index) => {
          const sliceSize = 100 / safeSlices;
          const startClipY = index * sliceSize;
          const endClipY = (index + 1) * sliceSize;

          layer.style.transition = `clip-path ${config.outDuration}ms ${config.easing}`;
          layer.style.transitionDelay = `${getSliceDelay(index, config.staggerOut)}ms`;
          layer.style.clipPath = `polygon(50% ${startClipY}%, 50% ${startClipY}%, 50% ${endClipY}%, 50% ${endClipY}%)`;
        });

        schedule(() => {
          activeImagesRef.current = activeImagesRef.current.filter((element) => element !== imageElement);
          imageElement.remove();
        }, config.outDuration + getMaxSliceDelay(config.staggerOut));
      }, config.imageLifespan);
    };

    const render = () => {
      if (pointerActiveRef.current) {
        interpolatedPointerPosRef.current = {
          x: interpolatedPointerPosRef.current.x + (pointerPosRef.current.x - interpolatedPointerPosRef.current.x) * safeSmoothing,
          y: interpolatedPointerPosRef.current.y + (pointerPosRef.current.y - interpolatedPointerPosRef.current.y) * safeSmoothing,
        };

        if (distanceFromLastSpawn() > safeSpawnThreshold) {
          lastSpawnPosRef.current = { ...interpolatedPointerPosRef.current };
          createTrailImage();
        }
      }

      animationFrameRef.current = requestAnimationFrame(render);
    };

    container.addEventListener("pointerenter", updatePointer);
    container.addEventListener("pointermove", updatePointer);
    container.addEventListener("pointerleave", handlePointerLeave);
    animationFrameRef.current = requestAnimationFrame(render);

    return () => {
      container.removeEventListener("pointerenter", updatePointer);
      container.removeEventListener("pointermove", updatePointer);
      container.removeEventListener("pointerleave", handlePointerLeave);

      if (animationFrameRef.current) {
        cancelAnimationFrame(animationFrameRef.current);
      }

      timeoutsRef.current.forEach((timeout) => clearTimeout(timeout));
      timeoutsRef.current = [];
      activeImagesRef.current = [];
      container.replaceChildren();
      pointerActiveRef.current = false;
    };
  }, [
    config.easing,
    config.imageLifespan,
    config.inDuration,
    config.outDuration,
    config.slideDuration,
    config.slideEasing,
    config.staggerIn,
    config.staggerOut,
    imageSize,
    slices,
    smoothing,
    spawnThreshold,
  ]);

  return (
    <div
      ref={containerRef}
      aria-hidden="true"
      className={cn("absolute inset-0 overflow-hidden pointer-events-auto touch-none", className)}
    />
  );
}

export default PixelatedImageTrail;

```

---

## `src\components\ui\pop-button.tsx`

```tsx
import React from "react";
import { cn } from "@/lib/utils";

export interface PopButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {}

export function PopButton({ className, children = "Learn More", ...props }: PopButtonProps) {
  return (
    <button
      className={cn(
        "group relative inline-flex items-center justify-center font-semibold uppercase text-[#382b22] dark:text-[#382b22]",
        "px-8 py-5 rounded-xl bg-[#fff0f0] border-2 border-[#b18597]",
        "transition-all duration-150 ease-[cubic-bezier(0,0,0.58,1)]",
        "shadow-[0_12px_0_-2px_#f9c4d2,0_12px_0_0_#b18597,0_22px_0_0_#ffe3e2]",
        "dark:shadow-[0_12px_0_-2px_#f9c4d2,0_12px_0_0_#b18597,0_22px_15px_-5px_rgba(0,0,0,0.3)]",
        "hover:bg-[#ffe9e9] hover:translate-y-1 hover:shadow-[0_8px_0_-2px_#f9c4d2,0_8px_0_0_#b18597,0_16px_0_0_#ffe3e2]",
        "dark:hover:shadow-[0_8px_0_-2px_#f9c4d2,0_8px_0_0_#b18597,0_16px_10px_-5px_rgba(0,0,0,0.3)]",
        "active:bg-[#ffe9e9] active:translate-y-3 active:shadow-[0_0px_0_-2px_#f9c4d2,0_0px_0_0_#b18597,0_0px_0_0_#ffe3e2]",
        "dark:active:shadow-[0_0px_0_-2px_#f9c4d2,0_0px_0_0_#b18597,0_0px_0_0_rgba(0,0,0,0)]",
        className
      )}
      {...props}
    >
      {children}
    </button>
  );
}

export default PopButton;

```

---

## `src\components\ui\popover.tsx`

```tsx
"use client"

import * as React from "react"
import * as PopoverPrimitive from "@radix-ui/react-popover"

import { cn } from "@/lib/utils"

function Popover({
  ...props
}: React.ComponentProps<typeof PopoverPrimitive.Root>) {
  return <PopoverPrimitive.Root data-slot="popover" {...props} />
}

function PopoverTrigger({
  ...props
}: React.ComponentProps<typeof PopoverPrimitive.Trigger>) {
  return <PopoverPrimitive.Trigger data-slot="popover-trigger" {...props} />
}

function PopoverContent({
  className,
  align = "center",
  sideOffset = 4,
  ...props
}: React.ComponentProps<typeof PopoverPrimitive.Content>) {
  return (
    <PopoverPrimitive.Portal>
      <PopoverPrimitive.Content
        data-slot="popover-content"
        align={align}
        sideOffset={sideOffset}
        className={cn(
          "bg-popover text-popover-foreground data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0 data-[state=closed]:zoom-out-95 data-[state=open]:zoom-in-95 data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-from-right-2 data-[side=right]:slide-in-from-left-2 data-[side=top]:slide-in-from-bottom-2 z-50 w-72 origin-(--radix-popover-content-transform-origin) rounded-md border p-4 shadow-md outline-hidden",
          className
        )}
        {...props}
      />
    </PopoverPrimitive.Portal>
  )
}

function PopoverAnchor({
  ...props
}: React.ComponentProps<typeof PopoverPrimitive.Anchor>) {
  return <PopoverPrimitive.Anchor data-slot="popover-anchor" {...props} />
}

export { Popover, PopoverTrigger, PopoverContent, PopoverAnchor }

```

---

## `src\components\ui\progress.tsx`

```tsx
"use client"

import * as React from "react"
import * as ProgressPrimitive from "@radix-ui/react-progress"

import { cn } from "@/lib/utils"

function Progress({
  className,
  value,
  ...props
}: React.ComponentProps<typeof ProgressPrimitive.Root>) {
  return (
    <ProgressPrimitive.Root
      data-slot="progress"
      className={cn(
        "bg-primary/20 relative h-2 w-full overflow-hidden rounded-full",
        className
      )}
      {...props}
    >
      <ProgressPrimitive.Indicator
        data-slot="progress-indicator"
        className="bg-primary h-full w-full flex-1 transition-all"
        style={{ transform: `translateX(-${100 - (value || 0)}%)` }}
      />
    </ProgressPrimitive.Root>
  )
}

export { Progress }

```

---

## `src\components\ui\radial-glow-button.tsx`

```tsx
"use client";

import React from "react";
import { cn } from "@/lib/utils";

export interface RadialGlowButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  children?: React.ReactNode;
}

export function RadialGlowButton({
  children = "Get Extension",
  className,
  ...props
}: RadialGlowButtonProps) {
  return (
    <div className="relative inline-block">
      <style>{`
        @property --rg-pos-x { syntax: '<percentage>'; initial-value: 40%; inherits: false; }
        @property --rg-pos-y { syntax: '<percentage>'; initial-value: 140%; inherits: false; }
        @property --rg-spread-x { syntax: '<percentage>'; initial-value: 130%; inherits: false; }
        @property --rg-spread-y { syntax: '<percentage>'; initial-value: 170%; inherits: false; }
        @property --rg-color-1 { syntax: '<color>'; initial-value: #000022; inherits: false; }
        @property --rg-color-2 { syntax: '<color>'; initial-value: #1f3f6d; inherits: false; }
        @property --rg-color-3 { syntax: '<color>'; initial-value: #469396; inherits: false; }
        @property --rg-color-4 { syntax: '<color>'; initial-value: #f1ffa5; inherits: false; }
        @property --rg-color-5 { syntax: '<color>'; initial-value: hsl(250 80% 2.5%); inherits: false; }
        @property --rg-border-angle { syntax: '<angle>'; initial-value: 180deg; inherits: true; }
        @property --rg-border-color-1 { syntax: '<color>'; initial-value: hsla(230, 75%, 90%, 0.7); inherits: true; }
        @property --rg-border-color-2 { syntax: '<color>'; initial-value: hsla(230, 50%, 90%, 0.25); inherits: true; }
        @property --rg-stop-1 { syntax: '<percentage>'; initial-value: 37.35%; inherits: false; }
        @property --rg-stop-2 { syntax: '<percentage>'; initial-value: 61.36%; inherits: false; }
        @property --rg-stop-3 { syntax: '<percentage>'; initial-value: 78.42%; inherits: false; }
        @property --rg-stop-4 { syntax: '<percentage>'; initial-value: 93.52%; inherits: false; }
        @property --rg-stop-5 { syntax: '<percentage>'; initial-value: 100%; inherits: false; }

        .rg-button {
          --transition: 0.25s;
          --spark: 1.8s;
          --speed: 1.2s;
          --cut: 1px;
          --bg: radial-gradient(
            var(--rg-spread-x) var(--rg-spread-y) at var(--rg-pos-x) var(--rg-pos-y),
            var(--rg-color-1) var(--rg-stop-1),
            var(--rg-color-2) var(--rg-stop-2),
            var(--rg-color-3) var(--rg-stop-3),
            var(--rg-color-4) var(--rg-stop-4),
            var(--rg-color-5) var(--rg-stop-5)
          );
          
          position: relative;
          min-width: 160px;
          min-height: 51px;
          padding: 16px 24px;
          border: none;
          border-radius: 11px;
          font-family: inherit;
          font-size: 16px;
          font-weight: 500;
          line-height: 19px;
          color: rgba(255, 255, 255, 0.95);
          background: var(--bg);
          cursor: pointer;
          text-shadow: 0 0 2px rgba(0, 0, 0, 0.95);
          overflow: hidden;
          -webkit-font-smoothing: antialiased;
          -webkit-tap-highlight-color: transparent;
          transition: 
            --rg-pos-x .75s, --rg-pos-y .75s,
            --rg-spread-x .75s, --rg-spread-y .75s,
            --rg-color-1 .75s, --rg-color-2 .75s, --rg-color-3 .75s, --rg-color-4 .75s, --rg-color-5 .75s,
            --rg-border-angle .75s, --rg-border-color-1 .75s, --rg-border-color-2 .75s,
            --rg-stop-1 .75s, --rg-stop-2 .75s, --rg-stop-3 .75s, --rg-stop-4 .75s, --rg-stop-5 .75s;
        }

        .rg-button::before {
          content: '';
          position: absolute;
          inset: 0;
          padding: 1px;
          border-radius: inherit;
          background-image: linear-gradient(var(--rg-border-angle), var(--rg-border-color-1), var(--rg-border-color-2));
          mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
          mask-composite: exclude;
          pointer-events: none;
        }

        .rg-button:hover {
          --rg-pos-x: 0%;
          --rg-pos-y: 120%;
          --rg-spread-x: 110.24%;
          --rg-spread-y: 110.2%;
          --rg-color-1: #000020;
          --rg-color-2: #f1ffa5;
          --rg-color-3: #469396;
          --rg-color-4: #1f3f6d;
          --rg-stop-1: 0%;
          --rg-stop-2: 10%;
          --rg-stop-3: 35.44%;
          --rg-stop-4: 71.34%;
          --rg-stop-5: 150%;
          --rg-border-angle: 190deg;
          --rg-border-color-1: hsla(320, 75%, 90%, 0.1);
          --rg-border-color-2: hsla(320, 50%, 90%, 0.35);
          --button-line-opacity: 1;
        }

        .rg-label {
          position: relative;
          z-index: 1;
        }

        .rg-bg {
          position: absolute;
          inset: var(--cut);
          background: var(--bg);
          border-radius: inherit;
          transition: background var(--transition), opacity var(--transition);
        }

        .rg-shine {
          position: absolute;
          inset: 0;
          container-type: size;
          border-radius: inherit;
          mix-blend-mode: soft-light;
          opacity: var(--button-line-opacity, 0);
          transition: opacity 0.3s;
          overflow: visible;
        }

        .rg-shine span {
          position: absolute;
          inset: 0;
          height: 100cqh;
          aspect-ratio: 1;
          animation: rg-slide var(--speed) ease-in-out infinite alternate;
          overflow: visible;
        }

        .rg-shine span::before {
          content: "";
          position: absolute;
          inset: -100%;
          background: conic-gradient(
            from calc(270deg - (90deg * 0.5)),
            transparent 0,
            #fff 90deg,
            transparent 90deg
          );
          animation: rg-spin calc(var(--speed) * 2) infinite linear;
        }

        @keyframes rg-spin {
          0% { rotate: 0deg; }
          15%, 35% { rotate: 90deg; }
          65%, 85% { rotate: 270deg; }
          100% { rotate: 360deg; }
        }

        @keyframes rg-slide {
          to { transform: translate(calc(100cqw - 100%), 0); }
        }
      `}</style>
      
      <button className={cn("rg-button", className)} type="button" {...props}>
        <span className="rg-shine">
          <span></span>
        </span>
        <span className="rg-bg"></span>
        <span className="rg-label">{children}</span>
      </button>
    </div>
  );
}

export default RadialGlowButton;

```

---

## `src\components\ui\radio-group.tsx`

```tsx
'use client'

import * as React from 'react'
import * as RadioGroupPrimitive from '@radix-ui/react-radio-group'
import { CircleIcon } from 'lucide-react'

import { cn } from '@/lib/utils'

function RadioGroup({ className, ...props }: React.ComponentProps<typeof RadioGroupPrimitive.Root>) {
    return (
        <RadioGroupPrimitive.Root
            data-slot="radio-group"
            className={cn('grid gap-3', className)}
            {...props}
        />
    )
}

function RadioGroupItem({ className, ...props }: React.ComponentProps<typeof RadioGroupPrimitive.Item>) {
    return (
        <RadioGroupPrimitive.Item
            data-slot="radio-group-item"
            className={cn('border-input text-primary ring-ring/10 dark:ring-ring/20 dark:outline-ring/40 outline-ring/50 shadow-xs aria-invalid:focus-visible:ring-0 aspect-square size-4 shrink-0 rounded-full border transition-[color,box-shadow] focus-visible:outline-1 focus-visible:ring-4 disabled:cursor-not-allowed disabled:opacity-50', className)}
            {...props}>
            <RadioGroupPrimitive.Indicator
                data-slot="radio-group-indicator"
                className="relative flex items-center justify-center">
                <CircleIcon className="fill-primary absolute left-1/2 top-1/2 size-2 -translate-x-1/2 -translate-y-1/2" />
            </RadioGroupPrimitive.Indicator>
        </RadioGroupPrimitive.Item>
    )
}

export { RadioGroup, RadioGroupItem }

```

---

## `src\components\ui\resizable.tsx`

```tsx
"use client"

import * as React from "react"
import { GripVerticalIcon } from "lucide-react"
import * as ResizablePrimitive from "react-resizable-panels"

import { cn } from "@/lib/utils"

function ResizablePanelGroup({
  className,
  ...props
}: React.ComponentProps<typeof ResizablePrimitive.Group>) {
  return (
    <ResizablePrimitive.Group
      data-slot="resizable-panel-group"
      className={cn(
        "flex h-full w-full data-[panel-group-direction=vertical]:flex-col",
        className
      )}
      {...props}
    />
  )
}

function ResizablePanel({
  ...props
}: React.ComponentProps<typeof ResizablePrimitive.Panel>) {
  return <ResizablePrimitive.Panel data-slot="resizable-panel" {...props} />
}

function ResizableHandle({
  withHandle,
  className,
  ...props
}: React.ComponentProps<typeof ResizablePrimitive.Separator> & {
  withHandle?: boolean
}) {
  return (
    <ResizablePrimitive.Separator
      data-slot="resizable-handle"
      className={cn(
        "bg-border focus-visible:ring-ring relative flex w-px items-center justify-center after:absolute after:inset-y-0 after:left-1/2 after:w-1 after:-translate-x-1/2 focus-visible:ring-1 focus-visible:ring-offset-1 focus-visible:outline-hidden data-[panel-group-direction=vertical]:h-px data-[panel-group-direction=vertical]:w-full data-[panel-group-direction=vertical]:after:left-0 data-[panel-group-direction=vertical]:after:h-1 data-[panel-group-direction=vertical]:after:w-full data-[panel-group-direction=vertical]:after:translate-x-0 data-[panel-group-direction=vertical]:after:-translate-y-1/2 [&[data-panel-group-direction=vertical]>div]:rotate-90",
        className
      )}
      {...props}
    >
      {withHandle && (
        <div className="bg-border z-10 flex h-4 w-3 items-center justify-center rounded-xs border">
          <GripVerticalIcon className="size-2.5" />
        </div>
      )}
    </ResizablePrimitive.Separator>
  )
}

export { ResizablePanelGroup, ResizablePanel, ResizableHandle }

```

---

## `src\components\ui\reveal-loader.tsx`

```tsx
"use client";
import { useGSAP } from "@gsap/react";
import gsap from "gsap";
import React, { useRef } from "react";
import { cn } from "@/lib/utils";
import { Anton } from "next/font/google";

const anton = Anton({
  weight: "400",
  subsets: ["latin"],
  display: "swap",
});

gsap.registerPlugin(useGSAP);

// --- EXPORTED TYPES ---
export type StaggerType = "left-to-right" | "right-to-left" | "center-out" | "edges-in";
export type MovementType = "top-down" | "bottom-up" | "fade-out" | "scale-vertical";

interface RevealLoaderProps {
  text?: string;
  textSize?: string;
  textColor?: string;
  bgColors?: string[];
  angle?: number;
  staggerOrder?: StaggerType;
  movementDirection?: MovementType;
  textFadeDelay?: number;
  className?: string;
  onComplete?: () => void;
}

const RevealLoader = ({
  text = "VENGEANCE",
  textSize = "100px",
  textColor = "white",
  bgColors = ["#000000"],
  angle = 0,
  staggerOrder = "left-to-right",
  movementDirection = "top-down",
  textFadeDelay = 0.5,
  className,
  onComplete,
}: RevealLoaderProps) => {
  const preloaderRef = useRef<HTMLDivElement>(null);

  const getBackgroundStyle = () => {
    if (bgColors.length === 0) return { backgroundColor: "black" };
    if (bgColors.length === 1) return { backgroundColor: bgColors[0] };
    return {
      backgroundImage: `linear-gradient(${angle}deg, ${bgColors.join(", ")})`,
    };
  };

  const getStaggerFrom = (type: StaggerType): string | number => {
    switch (type) {
      case "right-to-left": return "end";
      case "center-out": return "center";
      case "edges-in": return "edges";
      case "left-to-right":
      default: return "start";
    }
  };

  const getAnimationProperties = (type: MovementType) => {
    switch (type) {
      case "bottom-up":
        return { y: "-100%", ease: "power2.inOut" };
      case "fade-out":
        return { autoAlpha: 0, ease: "power2.inOut" };
      case "scale-vertical":
        return { scaleY: 0, transformOrigin: "center", ease: "power2.inOut" };
      case "top-down":
      default:
        return { y: "100%", ease: "power2.inOut" };
    }
  };

  useGSAP(
    () => {
      const tl = gsap.timeline({
        onComplete: onComplete,
      });

      const moveProps = getAnimationProperties(movementDirection);
      const staggerConfig = {
        each: 0.1,
        from: getStaggerFrom(staggerOrder) as any,
      };

      // 1. Reveal Text
      tl.to(".name-text span", {
        y: 0,
        stagger: 0.05,
        duration: 0.2,
        ease: "power2.out",
      });

      // 2. Animate Bars (The main structural animation)
      tl.to(".preloader-item", {
        delay: 1,
        duration: 0.5,
        stagger: staggerConfig,
        ...moveProps,
      })
        // 3. Fade Text (Relative to bars starting)
        .to(".name-text span", { autoAlpha: 0, duration: 0.3 }, `<${textFadeDelay}`)

        // 4. Hide Container (FIXED)
        // Removed ">" so it waits for the *entire timeline* (bars OR text) to finish.
        // Added "+=0.1" for a tiny buffer to prevent clipping.
        .to(preloaderRef.current, { autoAlpha: 0, duration: 0.1 }, "+=0.1");
    },
    { scope: preloaderRef, dependencies: [staggerOrder, movementDirection, textFadeDelay] },
  );

  return (
    <div
      className={cn(
        "absolute inset-0 z-[50] flex overflow-hidden bg-transparent",
        className,
      )}
      ref={preloaderRef}
    >
      {[...Array(10)].map((_, i) => (
        <div
          key={i}
          className="preloader-item h-full w-[10%]"
          style={getBackgroundStyle()}
        ></div>
      ))}

      <div className="absolute inset-0 flex items-center justify-center pointer-events-none">
        <div className="overflow-hidden">
          <p
            className={cn(
              "name-text flex leading-none tracking-tighter", // Removed mix-blend-difference
              anton.className
            )}
            style={{
              fontSize: textSize,
              color: textColor,
              fontWeight: "400",
              fontFeatureSettings: "normal",
              fontVariationSettings: "normal",
              textTransform: "uppercase",
              zIndex: 10, // Ensures text sits strictly on top
              position: "relative"
            }}
          >
            {text.split("").map((char, index) => (
              <span key={index} className="inline-block translate-y-full">
                {char === " " ? "\u00A0" : char}
              </span>
            ))}
          </p>
        </div>
      </div>
    </div>
  );
};

export default RevealLoader;
```

---

## `src\components\ui\ripple-displacement-slider.tsx`

```tsx
"use client";

import React, { useEffect, useRef, useState } from "react";
import * as THREE from "three";
import gsap from "gsap";
import { cn } from "@/lib/utils";

export interface RippleSlide {
  title: string;
  description: string;
  image: string;
}

export interface RippleDisplacementSliderProps {
  className?: string;
  slides?: RippleSlide[];
}

const DEFAULT_SLIDES: RippleSlide[] = [
  {
    title: "Goku",
    description: "The Earth's greatest defender. Constantly pushing past his limits to achieve new heights like Ultra Instinct.",
    image: "/goku.png", 
  },
  {
    title: "Luffy",
    description: "Captain of the Straw Hat Pirates. The Sun God Nika who brings smiles and liberates the seas with Gear 5.",
    image: "/luffy.png", 
  },
  {
    title: "Naruto",
    description: "The Hero of the Hidden Leaf. Wielding the massive power of the Nine-Tails and Six Paths Sage Mode.",
    image: "/naruto.png", 
  },
  {
    title: "Ichigo",
    description: "The Substitute Soul Reaper. Combining Hollow, Quincy, and Soul Reaper powers for an unstoppable True Bankai.",
    image: "/ichigo.png", 
  },
  {
    title: "Zoro",
    description: "The King of Hell. Master of the Three Sword Style, cutting through anything with terrifying conqueror's haki.",
    image: "/zoro.png", 
  },
  {
    title: "Aizen",
    description: "The ultimate mastermind. Standing at the pinnacle of power with his terrifying spiritual pressure and illusions.",
    image: "/aizen.png", 
  },
  {
    title: "Vegeta",
    description: "The proud Prince of all Saiyans. Fueled by his rivalry with Goku and his unyielding royal pride.",
    image: "/vegeta.png", 
  },
  {
    title: "Gohan",
    description: "Possesses hidden potential that surpasses even his father. When pushed to the edge, the Beast awakens.",
    image: "/gohan.png", 
  },
  {
    title: "Broly",
    description: "A mutant Saiyan of pure, unstoppable rage. His legendary power grows continuously during combat.",
    image: "/broly.png", 
  },
  {
    title: "Frieza",
    description: "The tyrannical emperor of Universe 7. A ruthless conqueror who always returns with a new, terrifying form.",
    image: "/frieza.png", 
  }
];

export const vertexShader = `
varying vec2 vUv;
void main() {
  vUv = uv;
  gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
}
`;

export const fragmentShader = `
uniform sampler2D uTexCurrent;
uniform sampler2D uTexNext;
uniform float uProgress;
uniform vec2 uResolution;
uniform vec2 uImageRes;
uniform float uWaveFreq;
uniform float uWavePow;
uniform float uWaveWidth;
uniform float uFalloff;
uniform float uBoostStrength;
uniform float uCrossfadeWidth;
uniform float uMobile;

varying vec2 vUv;

vec2 getImageUv(vec2 uv, vec2 screenRes, vec2 imgRes, vec2 boxMin, vec2 boxMax) {
  vec2 boxUv = (uv - boxMin) / (boxMax - boxMin);
  vec2 boxSize = (boxMax - boxMin) * screenRes;
  float boxAspect = boxSize.x / boxSize.y;
  float imgAspect = imgRes.x / imgRes.y;
  vec2 scale = vec2(1.0);
  if (boxAspect > imgAspect) {
    scale.y = imgAspect / boxAspect;
  } else {
    scale.x = boxAspect / imgAspect;
  }
  return (boxUv - 0.5) * scale + 0.5;
}

bool isInsideBox(vec2 uv, vec2 boxMin, vec2 boxMax) {
  return uv.x >= boxMin.x && uv.x <= boxMax.x && uv.y >= boxMin.y && uv.y <= boxMax.y;
}

void main() {
  vec2 boxMin = mix(vec2(0.25, 0.175), vec2(0.0), uMobile);
  vec2 boxMax = mix(vec2(0.75, 0.825), vec2(1.0), uMobile);

  float aspectRatio = uResolution.y / uResolution.x;
  vec2 coord = vec2(vUv.x, vUv.y * aspectRatio);
  vec2 center = vec2(0.5, 0.5 * aspectRatio);
  
  float dist = distance(coord, center);
  float time = uProgress;

  vec2 displaced = coord;
  float brightness = 0.0;
  float blend = 0.0;

  if (time > 0.001) {
    float trailing = dist - time;
    if (trailing < uWaveWidth && trailing < 0.0) {
      float age = -trailing;
      float decay = exp(-age * uFalloff);
      float wave = sin(age * uWaveFreq) * decay;
      vec2 direction = normalize(coord - center);
      displaced += direction * wave * uWavePow;
      brightness = abs(wave) * uBoostStrength * decay;
    }
    blend = smoothstep(0.0, uCrossfadeWidth, -trailing);
  }
  
  vec2 finalUv = vec2(displaced.x, displaced.y / aspectRatio);
  vec2 imageUv = getImageUv(finalUv, uResolution, uImageRes, boxMin, boxMax);
  
  vec4 currentColor = texture2D(uTexCurrent, imageUv);
  vec4 nextColor = texture2D(uTexNext, imageUv);

  vec4 color = mix(currentColor, nextColor, blend);
  color.rgb += color.rgb * brightness;

  if (!isInsideBox(finalUv, boxMin, boxMax)) {
    color = vec4(0.0);
  }

  gl_FragColor = color;
}
`;

const rippleConfig = {
  waveFreq: 25.0,
  wavePow: 0.035,
  waveWidth: 0.5,
  falloff: 10.0,
  boostStrength: 0.5,
  crossfadeWidth: 0.05,
  duration: 1.2,
  endValue: 1.0,
  ease: "power2.inOut",
};

export function RippleDisplacementSlider({ 
  className,
  slides = DEFAULT_SLIDES
}: RippleDisplacementSliderProps) {
  const containerRef = useRef<HTMLDivElement>(null);
  const canvasRef = useRef<HTMLCanvasElement>(null);
  const contentRef = useRef<HTMLDivElement>(null);
  
  const [currentIndex, setCurrentIndex] = useState(0);
  const [texturesLoaded, setTexturesLoaded] = useState(false);
  
  const materialRef = useRef<THREE.ShaderMaterial | null>(null);
  const texturesRef = useRef<THREE.Texture[]>([]);
  const isTransitioning = useRef(false);

  // Free alternative to SplitText
  const renderSplitTitle = (title: string) => {
    return title.split("").map((char, i) => (
      <span key={i} className="inline-block overflow-hidden relative">
        <span className="char inline-block will-change-transform relative">{char === " " ? "\u00A0" : char}</span>
      </span>
    ));
  };

  useEffect(() => {
    if (!canvasRef.current || !containerRef.current) return;

    const scene = new THREE.Scene();
    const camera = new THREE.OrthographicCamera(-0.5, 0.5, 0.5, -0.5, 0.01, 10);
    camera.position.z = 1;

    const renderer = new THREE.WebGLRenderer({ 
      canvas: canvasRef.current,
      antialias: true, 
      alpha: true 
    });
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    renderer.setClearColor(0x000000, 0);

    const loader = new THREE.TextureLoader();
    loader.setCrossOrigin("anonymous");
    
    // OPTIMIZATION: Only await the FIRST texture so it renders immediately.
    const loadFirstTexture = new Promise<THREE.Texture>((resolve) => {
      loader.load(
        slides[0].image, 
        (texture) => {
          texture.minFilter = THREE.LinearFilter;
          texture.magFilter = THREE.LinearFilter;
          texture.wrapS = THREE.ClampToEdgeWrapping;
          texture.wrapT = THREE.ClampToEdgeWrapping;
          resolve(texture);
        },
        undefined,
        (err) => {
          console.error("Failed to load first texture", err);
          resolve(new THREE.Texture()); // Resolve anyway so it doesn't hang
        }
      );
    });

    loadFirstTexture.then((firstTex) => {
      // Background load the rest seamlessly
      const allTextures = slides.map((slide, idx) => {
        if (idx === 0) return firstTex;
        const tex = loader.load(slide.image);
        tex.minFilter = THREE.LinearFilter;
        tex.magFilter = THREE.LinearFilter;
        tex.wrapS = THREE.ClampToEdgeWrapping;
        tex.wrapT = THREE.ClampToEdgeWrapping;
        return tex;
      });
      
      texturesRef.current = allTextures;
      
      const width = containerRef.current!.clientWidth;
      const height = containerRef.current!.clientHeight;
      const isMobile = width < 1000;

      const imgWidth = (firstTex.image as any)?.width || 1920;
      const imgHeight = (firstTex.image as any)?.height || 1080;

      const material = new THREE.ShaderMaterial({
        vertexShader,
        fragmentShader,
        uniforms: {
          uTexCurrent: { value: allTextures[0] },
          uTexNext: { value: allTextures[1 % slides.length] },
          uProgress: { value: 0 },
          uResolution: { value: new THREE.Vector2(width, height) },
          uImageRes: { value: new THREE.Vector2(imgWidth, imgHeight) },
          uWaveFreq: { value: rippleConfig.waveFreq },
          uWavePow: { value: rippleConfig.wavePow },
          uWaveWidth: { value: rippleConfig.waveWidth },
          uFalloff: { value: rippleConfig.falloff },
          uBoostStrength: { value: rippleConfig.boostStrength },
          uCrossfadeWidth: { value: rippleConfig.crossfadeWidth },
          uMobile: { value: isMobile ? 1.0 : 0.0 },
        },
        transparent: true,
      });
      materialRef.current = material;

      const geometry = new THREE.PlaneGeometry(1, 1);
      const mesh = new THREE.Mesh(geometry, material);
      scene.add(mesh);

      const resize = () => {
        if (!containerRef.current) return;
        const w = containerRef.current.clientWidth;
        const h = containerRef.current.clientHeight;
        renderer.setSize(w, h);
        material.uniforms.uResolution.value.set(w, h);
        material.uniforms.uMobile.value = w < 1000 ? 1.0 : 0.0;

        // Ensure the wave expands fully beyond the screen corners
        const ratio = h / w;
        const cx = 0.5;
        const cy = 0.5 * ratio;
        const maxDist = Math.sqrt(cx * cx + cy * cy);
        
        rippleConfig.endValue = maxDist + rippleConfig.waveWidth;
        rippleConfig.duration = w <= 1000 ? 0.9 : 1.2;
      };
      resize();
      window.addEventListener("resize", resize);

      gsap.ticker.add(() => {
        renderer.render(scene, camera);
      });

      setTexturesLoaded(true);

      // Trigger initial text in based on Screenshot 3
      const ctx = gsap.context(() => {
        const chars = document.querySelectorAll(".char");
        
        gsap.fromTo(
          chars,
          { y: "100%" },
          { y: "0%", duration: 0.8, stagger: 0.025, ease: "power2.out" }
        );
      }, contentRef);

      return () => {
        window.removeEventListener("resize", resize);
        gsap.ticker.remove(() => renderer.render(scene, camera));
        renderer.dispose();
        geometry.dispose();
        material.dispose();
        ctx.revert();
      };
    });
  }, [slides]);

  const handleNextSlide = () => {
    if (isTransitioning.current || !materialRef.current || !texturesRef.current.length) return;
    isTransitioning.current = true;

    const nextIndex = (currentIndex + 1) % slides.length;
    
    // Animate text out
    const chars = contentRef.current?.querySelectorAll(".char");
      
    const tl = gsap.timeline({
      onComplete: () => {
        setCurrentIndex(nextIndex);
        
        // Wait for React to render new text, then animate in
        setTimeout(() => {
          const newChars = contentRef.current?.querySelectorAll(".char");
          if (!newChars) return;
          
          gsap.set(newChars, { y: "100%" });
          
          gsap.to(newChars, {
            y: "0%",
            duration: 0.5,
            stagger: 0.015,
            ease: "power3.out",
          });
        }, 50);
      }
    });
      
    tl.to(chars || [], {
      y: "-100%",
      duration: 0.3,
      stagger: 0.01,
      ease: "power2.in",
    });

    // Animate shader
    const material = materialRef.current;
    material.uniforms.uTexNext.value = texturesRef.current[nextIndex];
    
    gsap.to(material.uniforms.uProgress, {
      value: rippleConfig.endValue,
      duration: rippleConfig.duration,
      ease: rippleConfig.ease,
      onComplete: () => {
        material.uniforms.uTexCurrent.value = texturesRef.current[nextIndex];
        material.uniforms.uProgress.value = 0;
        isTransitioning.current = false;
      }
    });
  };

  // Autoplay loop
  useEffect(() => {
    if (!texturesLoaded) return;
    const timer = setTimeout(() => {
      handleNextSlide();
    }, 5000);
    return () => clearTimeout(timer);
  }, [currentIndex, texturesLoaded]);

  return (
    <div 
      ref={containerRef} 
      className={cn("slider relative w-full h-full min-h-[600px] overflow-hidden bg-[#e0ddcf] dark:bg-black cursor-pointer", className)}
      onClick={handleNextSlide}
    >
      <canvas ref={canvasRef} className="block w-full h-full absolute inset-0 z-0" />
      
      <div 
        ref={contentRef}
        className="slide-content absolute inset-0 w-full h-full mix-blend-difference select-none pointer-events-none z-[2]"
        style={{ opacity: texturesLoaded ? 1 : 0, transition: "opacity 0.5s ease" }}
      >
        <div className="slide-title absolute top-1/2 left-12 -translate-y-1/2 w-max text-white max-[1000px]:top-1/2 max-[1000px]:left-1/2 max-[1000px]:-translate-x-1/2 max-[1000px]:-translate-y-1/2">
          <h1 className="text-[clamp(2rem,4vw,6rem)] font-medium tracking-[-0.02em] leading-tight">
            {renderSplitTitle(slides[currentIndex].title)}
          </h1>
        </div>
      </div>
    </div>
  );
}

export default RippleDisplacementSlider;

```

---

## `src\components\ui\scroll-area.tsx`

```tsx
'use client'

import * as React from 'react'
import * as ScrollAreaPrimitive from '@radix-ui/react-scroll-area'

import { cn } from '@/lib/utils'

function ScrollArea({ className, children, ...props }: React.ComponentProps<typeof ScrollAreaPrimitive.Root>) {
    return (
        <ScrollAreaPrimitive.Root
            data-slot="scroll-area"
            className={cn('relative', className)}
            {...props}>
            <ScrollAreaPrimitive.Viewport
                data-slot="scroll-area-viewport"
                className="focus-visible:ring-ring/50 size-full rounded-[inherit] outline-none transition-[color,box-shadow] focus-visible:outline-1 focus-visible:ring-[3px]">
                {children}
            </ScrollAreaPrimitive.Viewport>
            <ScrollBar />
            <ScrollAreaPrimitive.Corner />
        </ScrollAreaPrimitive.Root>
    )
}

function ScrollBar({ className, orientation = 'vertical', ...props }: React.ComponentProps<typeof ScrollAreaPrimitive.ScrollAreaScrollbar>) {
    return (
        <ScrollAreaPrimitive.ScrollAreaScrollbar
            data-slot="scroll-area-scrollbar"
            orientation={orientation}
            className={cn('flex touch-none select-none p-px transition-colors', orientation === 'vertical' && 'h-full w-2.5 border-l border-l-transparent', orientation === 'horizontal' && 'h-1.5 flex-col border-t border-t-transparent', className)}
            {...props}>
            <ScrollAreaPrimitive.ScrollAreaThumb
                data-slot="scroll-area-thumb"
                className="bg-border relative flex-1 rounded-full"
            />
        </ScrollAreaPrimitive.ScrollAreaScrollbar>
    )
}

export { ScrollArea, ScrollBar }

```

---

## `src\components\ui\scroll-dissolve-reveal.tsx`

```tsx
"use client";

import React, { useRef, useMemo } from "react";
import { Canvas, useFrame, useThree } from "@react-three/fiber";
import { useTexture, OrthographicCamera } from "@react-three/drei";
import * as THREE from "three";
import { useScroll } from "framer-motion";
import { cn } from "@/lib/utils";

const coverVertexShader = `
  varying vec2 vUv;
  void main() {
    vUv = uv;
    gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
  }
`;

const coverFragmentShader = `
  uniform sampler2D uTexture;
  uniform vec2 uResolution;
  uniform vec2 uImageResolution;
  uniform float uDissolve;
  uniform vec2 uCenter;
  uniform float uTime;
  uniform float uGrayscale;
  uniform float uEdgeIntensity;
  uniform float uEdgeBrightness;
  varying vec2 vUv;

  mat3 sobelX = mat3(
    -1.0, 0.0, 1.0,
    -2.0, 0.0, 2.0,
    -1.0, 0.0, 1.0
  );

  mat3 sobelY = mat3(
    -1.0, -2.0, -1.0,
     0.0,  0.0,  0.0,
     1.0,  2.0,  1.0
  );

  float getLuminance(vec3 color) {
    return dot(color, vec3(0.299, 0.587, 0.114));
  }

  float sobel(sampler2D tex, vec2 uv, vec2 texelSize) {
    float gx = 0.0;
    float gy = 0.0;

    for (int i = -1; i <= 1; i++) {
      for (int j = -1; j <= 1; j++) {
        vec2 offset = vec2(float(i), float(j)) * texelSize;
        float lum = getLuminance(texture2D(tex, uv + offset).rgb);
        gx += lum * sobelX[i + 1][j + 1];
        gy += lum * sobelY[i + 1][j + 1];
      }
    }

    return sqrt(gx * gx + gy * gy);
  }

  float hash(vec2 p) {
    return fract(sin(dot(p, vec2(127.1, 311.7))) * 43758.5453);
  }

  float noise(vec2 p) {
    vec2 i = floor(p);
    vec2 f = fract(p);
    f = f * f * (3.0 - 2.0 * f);
    
    float a = hash(i);
    float b = hash(i + vec2(1.0, 0.0));
    float c = hash(i + vec2(0.0, 1.0));
    float d = hash(i + vec2(1.0, 1.0));
    
    return mix(mix(a, b, f.x), mix(c, d, f.x), f.y);
  }

  float fbm(vec2 p) {
    float value = 0.0;
    float amplitude = 0.5;
    float frequency = 1.0;
    
    for (int i = 0; i < 5; i++) {
      value += amplitude * noise(p * frequency);
      amplitude *= 0.5;
      frequency *= 2.0;
    }
    
    return value;
  }

  void main() {
    vec2 ratio = vec2(
      min((uResolution.x / uResolution.y) / (uImageResolution.x / uImageResolution.y), 1.0),
      min((uResolution.y / uResolution.x) / (uImageResolution.y / uImageResolution.x), 1.0)
    );

    vec2 uv = vec2(
      vUv.x * ratio.x + (1.0 - ratio.x) * 0.5,
      vUv.y * ratio.y + (1.0 - ratio.y) * 0.5
    );

    vec4 texColor = texture2D(uTexture, uv);
    
    float gray = getLuminance(texColor.rgb);
    vec3 grayscaleColor = vec3(gray);
    texColor.rgb = mix(texColor.rgb, grayscaleColor, uGrayscale);
    
    vec2 centeredUv = vUv - uCenter;
    float aspect = uResolution.x / uResolution.y;
    centeredUv.x *= aspect;
    float dist = length(centeredUv);
    
    float angle = atan(centeredUv.y, centeredUv.x);
    
    float noiseScale = 6.0;
    vec2 pixelatedUv = floor(vUv * uResolution / noiseScale) * noiseScale / uResolution;
    float blockNoise = fbm(pixelatedUv * 100.0) * 0.15;
    
    float angularNoise = fbm(vec2(angle * 5.0, 0.0)) * 0.15;
    
    float totalNoise = blockNoise + angularNoise;
    float noisyDist = dist + totalNoise;
    
    float maxDist = length(vec2(aspect * 0.5, 0.5));
    float normalizedDist = noisyDist / maxDist;
    
    float dissolveThreshold = uDissolve * 1.5; 
    
    vec2 texelSize = 1.0 / uResolution;
    float edge = sobel(uTexture, uv, texelSize);
    
    edge = pow(edge, 0.7) * 2.0;
    edge = clamp(edge, 0.0, 1.0);
    
    float dissolveMask = smoothstep(dissolveThreshold - 0.03, dissolveThreshold, normalizedDist);
    
    vec3 edgeColor = vec3(1.0, 1.0, 1.0);
    
    vec3 baseColor = mix(texColor.rgb, vec3(0.0), uGrayscale);
    vec3 finalColor = baseColor;
    
    float edgeGlowIntensity = uEdgeIntensity * 2.0;
    float edgeGlow = edge * edgeGlowIntensity * (1.0 + uGrayscale * 3.0);
    finalColor += edgeColor * edgeGlow * uEdgeBrightness;
    
    float edgeZoneWidth = 0.15 * (1.0 - uDissolve) + 0.02;
    float edgeZone = smoothstep(dissolveThreshold - edgeZoneWidth, dissolveThreshold - edgeZoneWidth + 0.04, normalizedDist) * 
                     smoothstep(dissolveThreshold + 0.02, dissolveThreshold - 0.02, normalizedDist);
    float sparkle = hash(floor(vUv * uResolution / 4.0)) * edgeZone;
    
    float edgeBrightness = (1.0 - uDissolve) * uEdgeBrightness * (1.0 + uGrayscale * 2.0);
    finalColor += vec3(sparkle * 3.0 * edgeBrightness);
    
    float alpha = dissolveMask * texColor.a;

    gl_FragColor = vec4(finalColor, alpha);
  }
`;

const coverFragmentShaderReverse = `
  uniform sampler2D uTexture;
  uniform vec2 uResolution;
  uniform vec2 uImageResolution;
  uniform float uDissolve;
  uniform vec2 uCenter;
  uniform float uTime;
  uniform float uBrightness;
  uniform float uEdgeIntensity;
  uniform float uDarkness;
  uniform float uGrayscale;
  varying vec2 vUv;

  mat3 sobelX = mat3(
    -1.0, 0.0, 1.0,
    -2.0, 0.0, 2.0,
    -1.0, 0.0, 1.0
  );

  mat3 sobelY = mat3(
    -1.0, -2.0, -1.0,
     0.0,  0.0,  0.0,
     1.0,  2.0,  1.0
  );

  float getLuminance(vec3 color) {
    return dot(color, vec3(0.299, 0.587, 0.114));
  }

  float sobel(sampler2D tex, vec2 uv, vec2 texelSize) {
    float gx = 0.0;
    float gy = 0.0;

    for (int i = -1; i <= 1; i++) {
      for (int j = -1; j <= 1; j++) {
        vec2 offset = vec2(float(i), float(j)) * texelSize;
        float lum = getLuminance(texture2D(tex, uv + offset).rgb);
        gx += lum * sobelX[i + 1][j + 1];
        gy += lum * sobelY[i + 1][j + 1];
      }
    }

    return sqrt(gx * gx + gy * gy);
  }

  void main() {
    vec2 ratio = vec2(
      min((uResolution.x / uResolution.y) / (uImageResolution.x / uImageResolution.y), 1.0),
      min((uResolution.y / uResolution.x) / (uImageResolution.y / uImageResolution.x), 1.0)
    );

    vec2 uv = vec2(
      vUv.x * ratio.x + (1.0 - ratio.x) * 0.5,
      vUv.y * ratio.y + (1.0 - ratio.y) * 0.5
    );

    vec4 texColor = texture2D(uTexture, uv);
    
    float gray = getLuminance(texColor.rgb);
    vec3 grayscaleColor = vec3(gray);
    texColor.rgb = mix(texColor.rgb, grayscaleColor, uGrayscale);
    
    vec2 texelSize = 1.0 / uResolution;
    float edge = sobel(uTexture, uv, texelSize);
    
    edge = pow(edge, 0.7) * 2.0;
    edge = clamp(edge, 0.0, 1.0);
    
    vec3 edgeColor = vec3(1.0, 1.0, 1.0);
    
    vec3 darkBase = vec3(0.0);
    vec3 baseColor = mix(texColor.rgb, darkBase, uDarkness);
    
    float edgeGlow = edge * uEdgeIntensity * 2.0;
    baseColor += edgeColor * edgeGlow;
    
    vec3 finalColor = clamp(baseColor, 0.0, 1.0);

    gl_FragColor = vec4(finalColor, texColor.a);
  }
`;

interface SceneProps {
  imageFront: string;
  imageBack: string;
  scrollYProgress: any;
}

const Scene = ({ imageFront, imageBack, scrollYProgress }: SceneProps) => {
  const [texture1, texture2] = useTexture([imageFront, imageBack]);
  const material1Ref = useRef<THREE.ShaderMaterial>(null);
  const material2Ref = useRef<THREE.ShaderMaterial>(null);
  const { size } = useThree();

  const uniforms1 = useMemo(
    () => ({
      uTexture: { value: texture1 },
      uResolution: { value: new THREE.Vector2(size.width, size.height) },
      uImageResolution: {
        value: new THREE.Vector2((texture1.image as any).width, (texture1.image as any).height),
      },
      uDissolve: { value: 0.0 },
      uCenter: { value: new THREE.Vector2(0.5, 0.5) },
      uTime: { value: 0.0 },
      uGrayscale: { value: 0.0 },
      uEdgeIntensity: { value: 0.0 },
      uEdgeBrightness: { value: 1.0 },
    }),
    [texture1, size]
  );

  const uniforms2 = useMemo(
    () => ({
      uTexture: { value: texture2 },
      uResolution: { value: new THREE.Vector2(size.width, size.height) },
      uImageResolution: {
        value: new THREE.Vector2((texture2.image as any).width, (texture2.image as any).height),
      },
      uDissolve: { value: 0.0 },
      uCenter: { value: new THREE.Vector2(0.5, 0.5) },
      uTime: { value: 0.0 },
      uBrightness: { value: 0.0 },
      uEdgeIntensity: { value: 0.6 },
      uDarkness: { value: 1.0 },
      uGrayscale: { value: 1.0 },
    }),
    [texture2, size]
  );

  useFrame((state) => {
    const timeInSeconds = state.clock.getElapsedTime();
    const progress = scrollYProgress.get();

    if (material1Ref.current) {
      material1Ref.current.uniforms.uTime.value = timeInSeconds;
      material1Ref.current.uniforms.uResolution.value.set(
        size.width,
        size.height
      );

      material1Ref.current.uniforms.uDissolve.value = progress;
      const grayscaleProgress = Math.min(1.0, progress / 0.4);
      material1Ref.current.uniforms.uGrayscale.value = grayscaleProgress;
      material1Ref.current.uniforms.uEdgeIntensity.value = progress * 0.5;
      material1Ref.current.uniforms.uEdgeBrightness.value = 1.0 - progress;
    }

    if (material2Ref.current) {
      material2Ref.current.uniforms.uTime.value = timeInSeconds;
      material2Ref.current.uniforms.uResolution.value.set(
        size.width,
        size.height
      );

      const acceleratedProgress = Math.min(1.0, progress * 1.1);
      material2Ref.current.uniforms.uEdgeIntensity.value =
        0.6 * (1.0 - acceleratedProgress);
      material2Ref.current.uniforms.uDarkness.value =
        1.0 - acceleratedProgress;
      material2Ref.current.uniforms.uGrayscale.value =
        1.0 - acceleratedProgress;
    }
  });

  return (
    <>
      <mesh position={[0, 0, -0.1]}>
        <planeGeometry args={[2, 2]} />
        <shaderMaterial
          ref={material2Ref}
          vertexShader={coverVertexShader}
          fragmentShader={coverFragmentShaderReverse}
          uniforms={uniforms2}
          transparent={true}
        />
      </mesh>
      <mesh position={[0, 0, 0]}>
        <planeGeometry args={[2, 2]} />
        <shaderMaterial
          ref={material1Ref}
          vertexShader={coverVertexShader}
          fragmentShader={coverFragmentShader}
          uniforms={uniforms1}
          transparent={true}
        />
      </mesh>
    </>
  );
};

export interface ScrollDissolveRevealProps {
  imageFront: string;
  imageBack: string;
  className?: string;
  containerClassName?: string;
  scrollContainerRef?: React.RefObject<HTMLElement | null>;
}

export function ScrollDissolveReveal({
  imageFront,
  imageBack,
  className,
  containerClassName,
  scrollContainerRef,
}: ScrollDissolveRevealProps) {
  const containerRef = useRef<HTMLDivElement>(null);
  const { scrollYProgress } = useScroll({
    target: containerRef,
    offset: ["start start", "end end"],
    ...(scrollContainerRef && { container: scrollContainerRef })
  });

  return (
    <div
      ref={containerRef}
      className={cn("relative h-[300vh] w-full", containerClassName)}
    >
      <div className={cn("sticky top-0 h-screen w-full", className)}>
        <Canvas>
          <OrthographicCamera
            makeDefault
            manual
            left={-1}
            right={1}
            top={1}
            bottom={-1}
            near={0.1}
            far={10}
            position={[0, 0, 1]}
          />
          <React.Suspense fallback={null}>
            <Scene
              imageFront={imageFront}
              imageBack={imageBack}
              scrollYProgress={scrollYProgress}
            />
          </React.Suspense>
        </Canvas>
      </div>
    </div>
  );
}

```

---

## `src\components\ui\search-modal.tsx`

```tsx
"use client";

import * as React from "react";
import { useCallback, useEffect, useMemo, useRef, useState } from "react";
import {
  Buildings,
  ChatTeardropText,
  Checks,
  EnvelopeSimple,
  FileArrowDown,
  ListPlus,
  MagnifyingGlass,
  Plus,
  RadioButton,
  ShareFat,
  SlidersHorizontal,
  Users,
  X,
} from "@phosphor-icons/react";
import { cn } from "@/lib/utils";

/**
 * Search Modal
 *
 * A minimalist command-palette panel: a search bar that live-filters the result
 * list, removable "I'm looking for…" tags, people/results rows with per-row
 * actions, a quick-actions list with keyboard hints, and a files section.
 *
 * Ported from the vanilla "CodeGrid Modern Minimalist Search Modal" into a
 * single, self-contained, prop-driven React component. Every section is data
 * driven and optional, icons are Phosphor, and it adapts to light and dark mode.
 */

export interface SearchTag {
  label: string;
  icon?: React.ReactNode;
}

export interface SearchResultAction {
  icon: React.ReactNode;
  label?: string;
  onClick?: () => void;
}

export interface SearchResult {
  /** Primary label (also matched against the query). */
  name: string;
  /** Secondary text such as an email or context (also matched). */
  meta?: string;
  /** Avatar image URL. Falls back to a neutral circle. */
  avatar?: string;
  /** Link target for the row. */
  href?: string;
  /** Trailing action icons. */
  actions?: SearchResultAction[];
}

export interface QuickAction {
  label: string;
  icon?: React.ReactNode;
  /** Keyboard hint shown on the right (e.g. "E"). */
  shortcut?: string;
  onClick?: () => void;
}

export interface SearchFile {
  name: string;
  /** File extension shown dimmed after the name (e.g. ".pdf"). */
  ext?: string;
  icon?: React.ReactNode;
  /** Show the green "verified" checks next to the name. */
  verified?: boolean;
  onShare?: () => void;
}

export interface SearchModalProps {
  /** Placeholder for the search input. */
  placeholder?: string;
  /** Removable filter tags. */
  tags?: SearchTag[];
  /** Result rows, live-filtered by the query. */
  results?: SearchResult[];
  /** Quick-action rows. */
  quickActions?: QuickAction[];
  /** File rows. */
  files?: SearchFile[];
  /** Initial query value. */
  defaultQuery?: string;
  /** Called as the query changes. */
  onQueryChange?: (query: string) => void;
  /** Called when a result row is clicked. */
  onSelectResult?: (result: SearchResult, index: number) => void;
  /** Extra classes for the root panel. */
  className?: string;

  /** Render as a centered overlay with a backdrop instead of an inline panel. */
  modal?: boolean;
  /** Controlled open state (modal mode). */
  open?: boolean;
  /** Uncontrolled initial open state (modal mode). Defaults to false. */
  defaultOpen?: boolean;
  /** Called when the modal opens (true) or closes (false). */
  onOpenChange?: (open: boolean) => void;
  /** Key (pressed with ⌘/Ctrl) that toggles the modal. Defaults to "k"; set null to disable. */
  hotkey?: string | null;
  /** Close the modal on Escape. Defaults to true. */
  closeOnEscape?: boolean;
  /** Extra classes for the overlay wrapper (e.g. override `fixed` with `absolute` to scope it). */
  overlayClassName?: string;
}

const ICON = "h-[18px] w-[18px] text-neutral-400 dark:text-neutral-500";

const DEFAULT_TAGS: SearchTag[] = [
  { label: "Reactions", icon: <RadioButton className="h-4 w-4" /> },
  { label: "People", icon: <Users className="h-4 w-4" /> },
  { label: "Companies", icon: <Buildings className="h-4 w-4" /> },
];

const DEFAULT_RESULTS: SearchResult[] = [
  {
    name: "Jason Woordheart",
    meta: "jason@dribbble.com",
    actions: [
      { icon: <ChatTeardropText className="h-4 w-4" />, label: "Message" },
      { icon: <ListPlus className="h-4 w-4" />, label: "Add to list" },
    ],
  },
  {
    name: "Rob Miller",
    meta: "rob@icloud.com",
    actions: [{ icon: <ListPlus className="h-4 w-4" />, label: "Add to list" }],
  },
  {
    name: "Hannah Steward",
    meta: "replied on thread",
    actions: [
      { icon: <EnvelopeSimple className="h-4 w-4" />, label: "Email" },
      { icon: <ListPlus className="h-4 w-4" />, label: "Add to list" },
    ],
  },
];

const DEFAULT_QUICK_ACTIONS: QuickAction[] = [
  { label: "Create new task", shortcut: "E" },
  { label: "Create note", shortcut: "S" },
  { label: "Add member", shortcut: "R" },
];

const DEFAULT_FILES: SearchFile[] = [
  { name: "Invoice", ext: ".pdf", verified: true },
];

export function SearchModal({
  placeholder = "Search for action, people, instruments",
  tags = DEFAULT_TAGS,
  results = DEFAULT_RESULTS,
  quickActions = DEFAULT_QUICK_ACTIONS,
  files = DEFAULT_FILES,
  defaultQuery = "",
  onQueryChange,
  onSelectResult,
  className,
  modal = false,
  open,
  defaultOpen = false,
  onOpenChange,
  hotkey = "k",
  closeOnEscape = true,
  overlayClassName,
}: SearchModalProps) {
  const [query, setQuery] = useState(defaultQuery);
  const [activeTags, setActiveTags] = useState<SearchTag[]>(tags);

  const inputRef = useRef<HTMLInputElement>(null);

  // Open state — controlled via `open`, otherwise internal.
  const isControlled = open !== undefined;
  const [internalOpen, setInternalOpen] = useState(defaultOpen);
  const actualOpen = isControlled ? open : internalOpen;

  const setOpen = useCallback(
    (value: boolean) => {
      if (!isControlled) setInternalOpen(value);
      onOpenChange?.(value);
    },
    [isControlled, onOpenChange],
  );

  // Keep the latest open state / setter reachable from the one-time listener.
  const openRef = useRef(actualOpen);
  const setOpenRef = useRef(setOpen);
  useEffect(() => {
    openRef.current = actualOpen;
    setOpenRef.current = setOpen;
  });

  // ⌘K / Ctrl+K to toggle, Escape to close.
  useEffect(() => {
    if (!modal) return;
    const onKeyDown = (e: KeyboardEvent) => {
      if (hotkey && e.key.toLowerCase() === hotkey.toLowerCase() && (e.metaKey || e.ctrlKey)) {
        e.preventDefault();
        setOpenRef.current(!openRef.current);
      } else if (closeOnEscape && e.key === "Escape" && openRef.current) {
        setOpenRef.current(false);
      }
    };
    window.addEventListener("keydown", onKeyDown);
    return () => window.removeEventListener("keydown", onKeyDown);
  }, [modal, hotkey, closeOnEscape]);

  // Focus the input when the modal opens.
  useEffect(() => {
    if (modal && actualOpen) {
      const id = requestAnimationFrame(() => inputRef.current?.focus());
      return () => cancelAnimationFrame(id);
    }
  }, [modal, actualOpen]);

  const filteredResults = useMemo(() => {
    const q = query.trim().toLowerCase();
    if (!q) return results;
    return results.filter((r) => `${r.name} ${r.meta ?? ""}`.toLowerCase().includes(q));
  }, [query, results]);

  const handleQuery = (value: string) => {
    setQuery(value);
    onQueryChange?.(value);
  };

  const removeTag = (index: number) =>
    setActiveTags((prev) => prev.filter((_, i) => i !== index));

  const panel = (
    <div
      role={modal ? "dialog" : undefined}
      aria-modal={modal ? true : undefined}
      className={cn(
        "mx-auto w-full max-w-xl overflow-hidden rounded-2xl border backdrop-blur-xl",
        "border-black/[0.07] bg-white/85 text-neutral-900 shadow-[0_10px_40px_-14px_rgba(0,0,0,0.22)]",
        "dark:border-white/[0.08] dark:bg-neutral-900/85 dark:text-white dark:shadow-[0_18px_50px_-16px_rgba(0,0,0,0.7)]",
        className,
      )}
    >
      {/* Search bar */}
      <div className="flex items-center gap-1 border-b border-black/[0.06] px-4 py-3.5 dark:border-white/[0.06]">
        <MagnifyingGlass className={ICON} />
        <input
          ref={inputRef}
          type="text"
          value={query}
          onChange={(e) => handleQuery(e.target.value)}
          placeholder={placeholder}
          aria-label="Search"
          className="min-w-0 flex-1 bg-transparent px-3 text-sm text-current outline-none placeholder:text-neutral-400 dark:placeholder:text-neutral-500"
        />
        <div className="flex shrink-0 items-center gap-2.5">
          <button type="button" aria-label="Filters" className="text-neutral-400 transition-colors hover:text-neutral-700 dark:text-neutral-500 dark:hover:text-neutral-200">
            <SlidersHorizontal className="h-[18px] w-[18px]" />
          </button>
          <kbd className="flex items-center gap-0.5 rounded-md border border-black/[0.06] bg-black/[0.03] px-1.5 py-0.5 font-sans text-[11px] font-medium text-neutral-400 dark:border-white/[0.08] dark:bg-white/[0.06] dark:text-neutral-500">
            <span className="text-[13px] leading-none">⌘</span>
            {modal && hotkey ? hotkey.toUpperCase() : "F"}
          </kbd>
        </div>
      </div>

      {/* Tags */}
      {activeTags.length > 0 ? (
        <div className="border-b border-black/[0.06] px-4 py-3.5 dark:border-white/[0.06]">
          <span className="text-[13px] text-neutral-400 dark:text-neutral-500">I&apos;m looking for...</span>
          <div className="mt-3 flex flex-wrap gap-2">
            {activeTags.map((tag, i) => (
              <span
                key={`${tag.label}-${i}`}
                className="flex items-center gap-1.5 rounded-full bg-black/[0.04] py-1 pl-2.5 pr-2 text-[13px] ring-1 ring-inset ring-black/[0.04] dark:bg-white/[0.06] dark:ring-white/[0.06]"
              >
                {tag.icon}
                <span>{tag.label}</span>
                <button
                  type="button"
                  onClick={() => removeTag(i)}
                  aria-label={`Remove ${tag.label}`}
                  className="text-neutral-400 transition-colors hover:text-neutral-700 dark:text-neutral-500 dark:hover:text-neutral-200"
                >
                  <X className="h-3.5 w-3.5" />
                </button>
              </span>
            ))}
          </div>
        </div>
      ) : null}

      {/* Results */}
      {results.length > 0 ? (
        <div className="border-b border-black/[0.06] dark:border-white/[0.06]">
          <p className="px-4 pt-3.5 pb-2 text-[13px] text-neutral-400 dark:text-neutral-500">
            Last search&nbsp;&nbsp;<span className="text-neutral-600 dark:text-neutral-300">{filteredResults.length}</span>
          </p>
          <ul className="px-1.5 pb-1.5">
            {filteredResults.map((result, i) => (
              <li key={`${result.name}-${i}`}>
                <a
                  href={result.href ?? "#"}
                  onClick={() => onSelectResult?.(result, i)}
                  className="group relative flex items-center rounded-lg px-2.5 py-2 transition-colors hover:bg-black/[0.03] dark:hover:bg-white/[0.04]"
                >
                  {result.avatar ? (
                    // eslint-disable-next-line @next/next/no-img-element
                    <img
                      src={result.avatar}
                      alt=""
                      className="h-6 w-6 shrink-0 rounded-full object-cover ring-1 ring-black/5 dark:ring-white/10"
                    />
                  ) : (
                    <span className="h-6 w-6 shrink-0 rounded-full bg-neutral-300 ring-1 ring-black/5 dark:bg-neutral-600 dark:ring-white/10" />
                  )}
                  <span className="ml-2.5 truncate text-sm">
                    {result.name}
                    {result.meta ? <span className="pl-1.5 text-neutral-400 dark:text-neutral-500">{result.meta}</span> : null}
                  </span>
                  {result.actions && result.actions.length > 0 ? (
                    <span className="ml-auto flex items-center gap-2.5 pl-3 text-neutral-400 opacity-70 transition-opacity group-hover:opacity-100 dark:text-neutral-500">
                      {result.actions.map((action, ai) => (
                        <button
                          key={ai}
                          type="button"
                          aria-label={action.label}
                          onClick={(e) => {
                            e.preventDefault();
                            action.onClick?.();
                          }}
                          className="transition-colors hover:text-neutral-700 dark:hover:text-neutral-200"
                        >
                          {action.icon}
                        </button>
                      ))}
                    </span>
                  ) : null}
                </a>
              </li>
            ))}
          </ul>
        </div>
      ) : null}

      {/* Quick actions */}
      {quickActions.length > 0 ? (
        <div className="border-b border-black/[0.06] px-1.5 py-1.5 dark:border-white/[0.06]">
          <p className="px-2.5 pt-2 pb-1 text-[13px] text-neutral-400 dark:text-neutral-500">Quick actions</p>
          {quickActions.map((action, i) => (
            <button
              key={`${action.label}-${i}`}
              type="button"
              onClick={action.onClick}
              className="relative flex w-full items-center rounded-lg px-2.5 py-2 text-left transition-colors hover:bg-black/[0.03] dark:hover:bg-white/[0.04]"
            >
              <span className="flex h-[30px] w-[30px] shrink-0 items-center justify-center rounded-lg bg-black/[0.04] text-neutral-500 dark:bg-white/[0.06] dark:text-neutral-300">
                {action.icon ?? <Plus className="h-[15px] w-[15px]" />}
              </span>
              <span className="pl-3 text-sm">{action.label}</span>
              {action.shortcut ? (
                <kbd className="ml-auto flex h-[26px] w-[26px] items-center justify-center rounded-md bg-black/[0.04] font-sans text-[13px] text-neutral-500 ring-1 ring-inset ring-black/[0.04] dark:bg-white/[0.06] dark:text-neutral-300 dark:ring-white/[0.06]">
                  {action.shortcut}
                </kbd>
              ) : null}
            </button>
          ))}
        </div>
      ) : null}

      {/* Files */}
      {files.length > 0 ? (
        <div className="px-1.5 py-1.5">
          <p className="px-2.5 pt-2 pb-1 text-[13px] text-neutral-400 dark:text-neutral-500">
            Files&nbsp;&nbsp;<span className="text-neutral-600 dark:text-neutral-300">{files.length}</span>
          </p>
          {files.map((file, i) => (
            <div
              key={`${file.name}-${i}`}
              className="group relative flex items-center rounded-lg px-2.5 py-2 transition-colors hover:bg-black/[0.03] dark:hover:bg-white/[0.04]"
            >
              <span className="flex h-[30px] w-[30px] shrink-0 items-center justify-center rounded-lg bg-black/[0.04] text-neutral-500 dark:bg-white/[0.06] dark:text-neutral-300">
                {file.icon ?? <FileArrowDown className="h-[15px] w-[15px]" />}
              </span>
              <span className="flex items-center gap-1.5 pl-3 text-sm">
                <span>
                  {file.name}
                  {file.ext ? <span className="text-neutral-400 dark:text-neutral-500">{file.ext}</span> : null}
                </span>
                {file.verified ? <Checks className="h-4 w-4 text-emerald-500" /> : null}
              </span>
              <button
                type="button"
                onClick={file.onShare}
                className="ml-auto flex items-center gap-1.5 text-sm text-neutral-400 opacity-80 transition-all hover:text-neutral-700 group-hover:opacity-100 dark:text-neutral-500 dark:hover:text-neutral-200"
              >
                <ShareFat weight="bold" className="h-4 w-4" />
                <span>Share</span>
              </button>
            </div>
          ))}
        </div>
      ) : null}
    </div>
  );

  if (!modal) return panel;

  return (
    <div
      onClick={() => setOpen(false)}
      aria-hidden={!actualOpen}
      className={cn(
        "fixed inset-0 z-50 flex items-start justify-center p-4 pt-[12vh] transition-opacity duration-200",
        actualOpen ? "opacity-100" : "pointer-events-none opacity-0",
        overlayClassName,
      )}
    >
      <div className="absolute inset-0 bg-black/40 backdrop-blur-sm dark:bg-black/60" />
      <div
        onClick={(e) => e.stopPropagation()}
        className={cn(
          "relative z-10 w-full max-w-xl transition-all duration-200 ease-out",
          actualOpen ? "translate-y-0 scale-100 opacity-100" : "-translate-y-2 scale-[0.98] opacity-0",
        )}
      >
        {panel}
      </div>
    </div>
  );
}

export default SearchModal;

```

---

## `src\components\ui\select.tsx`

```tsx
'use client'

import * as React from 'react'
import * as SelectPrimitive from '@radix-ui/react-select'
import { CheckIcon, ChevronDownIcon, ChevronsUpDown, ChevronUpIcon } from 'lucide-react'

import { cn } from '@/lib/utils'

function Select({ ...props }: React.ComponentProps<typeof SelectPrimitive.Root>) {
    return (
        <SelectPrimitive.Root
            data-slot="select"
            {...props}
        />
    )
}

function SelectGroup({ ...props }: React.ComponentProps<typeof SelectPrimitive.Group>) {
    return (
        <SelectPrimitive.Group
            data-slot="select-group"
            {...props}
        />
    )
}

function SelectValue({ ...props }: React.ComponentProps<typeof SelectPrimitive.Value>) {
    return (
        <SelectPrimitive.Value
            data-slot="select-value"
            {...props}
        />
    )
}

function SelectTrigger({ className, children, ...props }: React.ComponentProps<typeof SelectPrimitive.Trigger>) {
    return (
        <SelectPrimitive.Trigger
            data-slot="select-trigger"
            className={cn(
                "border-input data-[placeholder]:text-muted-foreground aria-invalid:border-destructive ring-ring/10 dark:ring-ring/20 dark:outline-ring/40 outline-ring/50 [&_svg:not([class*='text-'])]:text-muted-foreground shadow-xs aria-invalid:focus-visible:ring-0 flex h-9 w-full items-center justify-between rounded-md border bg-transparent px-3 py-2 text-sm transition-[color,box-shadow] focus-visible:outline-1 focus-visible:ring-4 disabled:cursor-not-allowed disabled:opacity-50 *:data-[slot=select-value]:flex *:data-[slot=select-value]:items-center *:data-[slot=select-value]:gap-2 [&>span]:line-clamp-1 [&_svg:not([class*='size-'])]:size-4 [&_svg]:pointer-events-none [&_svg]:shrink-0",
                className
            )}
            {...props}>
            {children}
            <SelectPrimitive.Icon
                data-slot="select-icon"
                asChild>
                <ChevronsUpDown className="size-3 opacity-50" />
            </SelectPrimitive.Icon>
        </SelectPrimitive.Trigger>
    )
}

function SelectContent({ className, children, position = 'popper', ...props }: React.ComponentProps<typeof SelectPrimitive.Content>) {
    return (
        <SelectPrimitive.Portal>
            <SelectPrimitive.Content
                data-slot="select-content"
                className={cn(
                    'bg-popover text-popover-foreground data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0 data-[state=closed]:zoom-out-95 data-[state=open]:zoom-in-95 data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-from-right-2 data-[side=right]:slide-in-from-left-2 data-[side=top]:slide-in-from-bottom-2 relative z-50 max-h-96 min-w-[8rem] overflow-hidden rounded-md border shadow-md',
                    position === 'popper' && 'data-[side=bottom]:translate-y-1 data-[side=left]:-translate-x-1 data-[side=right]:translate-x-1 data-[side=top]:-translate-y-1',
                    className
                )}
                position={position}
                {...props}>
                <SelectScrollUpButton />
                <SelectPrimitive.Viewport
                    data-slot="select-viewport"
                    className={cn(position === 'popper' && 'h-[var(--radix-select-trigger-height)] w-full min-w-[var(--radix-select-trigger-width)] scroll-my-1')}>
                    {children}
                </SelectPrimitive.Viewport>
                <SelectScrollDownButton />
            </SelectPrimitive.Content>
        </SelectPrimitive.Portal>
    )
}

function SelectLabel({ className, ...props }: React.ComponentProps<typeof SelectPrimitive.Label>) {
    return (
        <SelectPrimitive.Label
            data-slot="select-label"
            className={cn('px-2 py-1.5 text-sm font-semibold', className)}
            {...props}
        />
    )
}

function SelectItem({ className, children, ...props }: React.ComponentProps<typeof SelectPrimitive.Item>) {
    return (
        <SelectPrimitive.Item
            data-slot="select-item"
            className={cn("focus:bg-accent focus:text-accent-foreground [&_svg:not([class*='text-'])]:text-muted-foreground outline-hidden *:[span]:last:flex *:[span]:last:items-center *:[span]:last:gap-2 relative flex w-full cursor-default select-none items-center gap-2 rounded-sm py-1.5 pl-2 pr-8 text-sm data-[disabled]:pointer-events-none data-[disabled]:opacity-50 [&_svg:not([class*='size-'])]:size-4 [&_svg]:pointer-events-none [&_svg]:shrink-0", className)}
            {...props}>
            <span
                data-slot="select-item-indicator"
                className="absolute right-2 flex size-3.5 items-center justify-center">
                <SelectPrimitive.ItemIndicator>
                    <CheckIcon
                        className="size-3"
                        strokeWidth={3}
                    />
                </SelectPrimitive.ItemIndicator>
            </span>
            <SelectPrimitive.ItemText>{children}</SelectPrimitive.ItemText>
        </SelectPrimitive.Item>
    )
}

function SelectSeparator({ className, ...props }: React.ComponentProps<typeof SelectPrimitive.Separator>) {
    return (
        <SelectPrimitive.Separator
            data-slot="select-separator"
            className={cn('bg-border pointer-events-none -mx-1 my-1 h-px', className)}
            {...props}
        />
    )
}

function SelectScrollUpButton({ className, ...props }: React.ComponentProps<typeof SelectPrimitive.ScrollUpButton>) {
    return (
        <SelectPrimitive.ScrollUpButton
            data-slot="select-scroll-up-button"
            className={cn('flex cursor-default items-center justify-center py-1', className)}
            {...props}>
            <ChevronUpIcon className="size-4" />
        </SelectPrimitive.ScrollUpButton>
    )
}

function SelectScrollDownButton({ className, ...props }: React.ComponentProps<typeof SelectPrimitive.ScrollDownButton>) {
    return (
        <SelectPrimitive.ScrollDownButton
            data-slot="select-scroll-down-button"
            className={cn('flex cursor-default items-center justify-center py-1', className)}
            {...props}>
            <ChevronDownIcon className="size-4" />
        </SelectPrimitive.ScrollDownButton>
    )
}

export { Select, SelectContent, SelectGroup, SelectItem, SelectLabel, SelectScrollDownButton, SelectScrollUpButton, SelectSeparator, SelectTrigger, SelectValue }

```

---

## `src\components\ui\separator.tsx`

```tsx
'use client'

import * as React from 'react'
import * as SeparatorPrimitive from '@radix-ui/react-separator'

import { cn } from '@/lib/utils'

function Separator({ className, orientation = 'horizontal', decorative = true, ...props }: React.ComponentProps<typeof SeparatorPrimitive.Root>) {
    return (
        <SeparatorPrimitive.Root
            data-slot="separator-root"
            decorative={decorative}
            orientation={orientation}
            className={cn('bg-border shrink-0 data-[orientation=horizontal]:h-px data-[orientation=vertical]:h-full data-[orientation=horizontal]:w-full data-[orientation=vertical]:w-px', className)}
            {...props}
        />
    )
}

export { Separator }

```

---

## `src\components\ui\shared-tooltip-avatars.tsx`

```tsx
"use client";

import React, { useState, useRef } from "react";
import { motion, AnimatePresence } from "framer-motion";
import { cn } from "@/lib/utils";

export interface AvatarItem {
  id: string;
  name: string;
  image: string;
}

export interface SharedTooltipAvatarsProps extends React.HTMLAttributes<HTMLDivElement> {
  items: AvatarItem[];
}

export function SharedTooltipAvatars({ 
  items, 
  className, 
  ...props 
}: SharedTooltipAvatarsProps) {
  const [hoveredIndex, setHoveredIndex] = useState<number | null>(null);
  const [tooltipPos, setTooltipPos] = useState({ left: 0, top: 0 });
  const [activeName, setActiveName] = useState("");
  
  const timeoutRef = useRef<NodeJS.Timeout | null>(null);
  const avatarRefs = useRef<(HTMLDivElement | null)[]>([]);

  const handleMouseEnter = (index: number) => {
    if (timeoutRef.current) clearTimeout(timeoutRef.current);

    const avatar = avatarRefs.current[index];
    if (avatar) {
      const left = avatar.offsetLeft + avatar.offsetWidth / 2;
      const top = avatar.offsetTop;

      setTooltipPos({ left, top });
      setActiveName(items[index].name);
      setHoveredIndex(index);
    }
  };

  const handleMouseLeave = () => {
    if (timeoutRef.current) clearTimeout(timeoutRef.current);
    timeoutRef.current = setTimeout(() => {
      setHoveredIndex(null);
    }, 150);
  };

  return (
    <div 
      className={cn("relative flex items-center justify-center py-12", className)} 
      {...props}
    >
      <AnimatePresence>
        {hoveredIndex !== null && (
          <motion.div
            initial={{ opacity: 0, x: "-50%", y: "-80%", scale: 0.95, left: tooltipPos.left, top: tooltipPos.top - 12 }}
            animate={{ 
              opacity: 1, 
              x: "-50%", 
              y: "-100%", 
              scale: 1,
              left: tooltipPos.left,
              top: tooltipPos.top - 12
            }}
            exit={{ opacity: 0, x: "-50%", y: "-80%", scale: 0.95 }}
            transition={{ type: "spring", stiffness: 300, damping: 25 }}
            className="absolute z-50 px-3.5 py-1.5 bg-white/70 dark:bg-neutral-900/70 backdrop-blur-xl rounded-xl text-sm font-medium text-neutral-900 dark:text-neutral-100 whitespace-nowrap shadow-[0_8px_30px_rgb(0,0,0,0.12)] dark:shadow-[0_8px_30px_rgb(0,0,0,0.2)] border border-neutral-200/50 dark:border-neutral-800/50 pointer-events-none"
            role="tooltip"
          >
            <AnimatePresence mode="wait">
              <motion.span
                key={activeName}
                initial={{ opacity: 0, y: 4 }}
                animate={{ opacity: 1, y: 0 }}
                exit={{ opacity: 0, y: -4 }}
                transition={{ duration: 0.15, ease: "easeOut" }}
                className="inline-block"
              >
                {activeName || " "}
              </motion.span>
            </AnimatePresence>
          </motion.div>
        )}
      </AnimatePresence>

      {/* Avatar Stack */}
      {items.map((item, index) => (
        <div
          key={item.id}
          ref={(el) => { avatarRefs.current[index] = el; }}
          className="relative -ml-3 first:ml-0 transition-transform duration-300 ease-out hover:-translate-y-2 hover:z-10 cursor-pointer"
          onMouseEnter={() => handleMouseEnter(index)}
          onMouseLeave={handleMouseLeave}
          onFocus={() => handleMouseEnter(index)}
          onBlur={handleMouseLeave}
          tabIndex={0}
          role="listitem"
        >
          <img
            src={item.image}
            alt={item.name}
            className="w-12 h-12 md:w-14 md:h-14 rounded-full object-cover border-[3px] border-white dark:border-[#09090b] shadow-sm hover:shadow-xl transition-all duration-300"
          />
        </div>
      ))}
    </div>
  );
}

export default SharedTooltipAvatars;

```

---

## `src\components\ui\sheet.tsx`

```tsx
"use client"

import * as React from "react"
import * as SheetPrimitive from "@radix-ui/react-dialog"
import { XIcon } from "lucide-react"

import { cn } from "@/lib/utils"

function Sheet({ ...props }: React.ComponentProps<typeof SheetPrimitive.Root>) {
  return <SheetPrimitive.Root data-slot="sheet" {...props} />
}

function SheetTrigger({
  ...props
}: React.ComponentProps<typeof SheetPrimitive.Trigger>) {
  return <SheetPrimitive.Trigger data-slot="sheet-trigger" {...props} />
}

function SheetClose({
  ...props
}: React.ComponentProps<typeof SheetPrimitive.Close>) {
  return <SheetPrimitive.Close data-slot="sheet-close" {...props} />
}

function SheetPortal({
  ...props
}: React.ComponentProps<typeof SheetPrimitive.Portal>) {
  return <SheetPrimitive.Portal data-slot="sheet-portal" {...props} />
}

function SheetOverlay({
  className,
  ...props
}: React.ComponentProps<typeof SheetPrimitive.Overlay>) {
  return (
    <SheetPrimitive.Overlay
      data-slot="sheet-overlay"
      className={cn(
        "data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0 fixed inset-0 z-50 bg-black/50",
        className
      )}
      {...props}
    />
  )
}

function SheetContent({
  className,
  children,
  side = "right",
  ...props
}: React.ComponentProps<typeof SheetPrimitive.Content> & {
  side?: "top" | "right" | "bottom" | "left"
}) {
  return (
    <SheetPortal>
      <SheetOverlay />
      <SheetPrimitive.Content
        data-slot="sheet-content"
        className={cn(
          "bg-background data-[state=open]:animate-in data-[state=closed]:animate-out fixed z-50 flex flex-col gap-4 shadow-lg transition ease-in-out data-[state=closed]:duration-300 data-[state=open]:duration-500",
          side === "right" &&
            "data-[state=closed]:slide-out-to-right data-[state=open]:slide-in-from-right inset-y-0 right-0 h-full w-3/4 border-l sm:max-w-sm",
          side === "left" &&
            "data-[state=closed]:slide-out-to-left data-[state=open]:slide-in-from-left inset-y-0 left-0 h-full w-3/4 border-r sm:max-w-sm",
          side === "top" &&
            "data-[state=closed]:slide-out-to-top data-[state=open]:slide-in-from-top inset-x-0 top-0 h-auto border-b",
          side === "bottom" &&
            "data-[state=closed]:slide-out-to-bottom data-[state=open]:slide-in-from-bottom inset-x-0 bottom-0 h-auto border-t",
          className
        )}
        {...props}
      >
        {children}
        <SheetPrimitive.Close className="ring-offset-background focus:ring-ring data-[state=open]:bg-secondary absolute top-4 right-4 rounded-xs opacity-70 transition-opacity hover:opacity-100 focus:ring-2 focus:ring-offset-2 focus:outline-hidden disabled:pointer-events-none">
          <XIcon className="size-4" />
          <span className="sr-only">Close</span>
        </SheetPrimitive.Close>
      </SheetPrimitive.Content>
    </SheetPortal>
  )
}

function SheetHeader({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="sheet-header"
      className={cn("flex flex-col gap-1.5 p-4", className)}
      {...props}
    />
  )
}

function SheetFooter({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="sheet-footer"
      className={cn("mt-auto flex flex-col gap-2 p-4", className)}
      {...props}
    />
  )
}

function SheetTitle({
  className,
  ...props
}: React.ComponentProps<typeof SheetPrimitive.Title>) {
  return (
    <SheetPrimitive.Title
      data-slot="sheet-title"
      className={cn("text-foreground font-semibold", className)}
      {...props}
    />
  )
}

function SheetDescription({
  className,
  ...props
}: React.ComponentProps<typeof SheetPrimitive.Description>) {
  return (
    <SheetPrimitive.Description
      data-slot="sheet-description"
      className={cn("text-muted-foreground text-sm", className)}
      {...props}
    />
  )
}

export {
  Sheet,
  SheetTrigger,
  SheetClose,
  SheetContent,
  SheetHeader,
  SheetFooter,
  SheetTitle,
  SheetDescription,
}

```

---

## `src\components\ui\sidebar.tsx`

```tsx
"use client";

import * as React from "react";
import { Slot } from "@radix-ui/react-slot";
import { cva, type VariantProps } from "class-variance-authority";
import { PanelLeftIcon } from "lucide-react";

import { useIsMobile } from "@/hooks/use-mobile";
import { cn } from "@/lib/utils";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Separator } from "@/components/ui/separator";
import {
  Sheet,
  SheetContent,
  SheetDescription,
  SheetHeader,
  SheetTitle,
} from "@/components/ui/sheet";
import { Skeleton } from "@/components/ui/skeleton";
import {
  Tooltip,
  TooltipContent,
  TooltipProvider,
  TooltipTrigger,
} from "@/components/ui/tooltip";

const SIDEBAR_COOKIE_NAME = "sidebar_state";
const SIDEBAR_COOKIE_MAX_AGE = 60 * 60 * 24 * 7;
const SIDEBAR_WIDTH = "16rem";
const SIDEBAR_WIDTH_MOBILE = "18rem";
const SIDEBAR_WIDTH_ICON = "3rem";
const SIDEBAR_KEYBOARD_SHORTCUT = "b";

type SidebarContextProps = {
  state: "expanded" | "collapsed";
  open: boolean;
  setOpen: (open: boolean) => void;
  openMobile: boolean;
  setOpenMobile: (open: boolean) => void;
  isMobile: boolean;
  toggleSidebar: () => void;
};

const SidebarContext = React.createContext<SidebarContextProps | null>(null);

function useSidebar() {
  const context = React.useContext(SidebarContext);
  if (!context) {
    throw new Error("useSidebar must be used within a SidebarProvider.");
  }

  return context;
}

function SidebarProvider({
  defaultOpen = true,
  open: openProp,
  onOpenChange: setOpenProp,
  className,
  style,
  children,
  ...props
}: React.ComponentProps<"div"> & {
  defaultOpen?: boolean;
  open?: boolean;
  onOpenChange?: (open: boolean) => void;
}) {
  const isMobile = useIsMobile();
  const [openMobile, setOpenMobile] = React.useState(false);

  // This is the internal state of the sidebar.
  // We use openProp and setOpenProp for control from outside the component.
  const [_open, _setOpen] = React.useState(defaultOpen);
  const open = openProp ?? _open;
  const setOpen = React.useCallback(
    (value: boolean | ((value: boolean) => boolean)) => {
      const openState = typeof value === "function" ? value(open) : value;
      if (setOpenProp) {
        setOpenProp(openState);
      } else {
        _setOpen(openState);
      }

      // This sets the cookie to keep the sidebar state.
      document.cookie = `${SIDEBAR_COOKIE_NAME}=${openState}; path=/; max-age=${SIDEBAR_COOKIE_MAX_AGE}`;
    },
    [setOpenProp, open]
  );

  // Helper to toggle the sidebar.
  const toggleSidebar = React.useCallback(() => {
    return isMobile ? setOpenMobile((open) => !open) : setOpen((open) => !open);
  }, [isMobile, setOpen, setOpenMobile]);

  // Adds a keyboard shortcut to toggle the sidebar.
  React.useEffect(() => {
    const handleKeyDown = (event: KeyboardEvent) => {
      if (
        event.key === SIDEBAR_KEYBOARD_SHORTCUT &&
        (event.metaKey || event.ctrlKey)
      ) {
        event.preventDefault();
        toggleSidebar();
      }
    };

    window.addEventListener("keydown", handleKeyDown);
    return () => window.removeEventListener("keydown", handleKeyDown);
  }, [toggleSidebar]);

  // We add a state so that we can do data-state="expanded" or "collapsed".
  // This makes it easier to style the sidebar with Tailwind classes.
  const state = open ? "expanded" : "collapsed";

  const contextValue = React.useMemo<SidebarContextProps>(
    () => ({
      state,
      open,
      setOpen,
      isMobile,
      openMobile,
      setOpenMobile,
      toggleSidebar,
    }),
    [state, open, setOpen, isMobile, openMobile, setOpenMobile, toggleSidebar]
  );

  return (
    <SidebarContext.Provider value={contextValue}>
      <TooltipProvider delayDuration={0}>
        <div
          data-slot="sidebar-wrapper"
          style={
            {
              "--sidebar-width": SIDEBAR_WIDTH,
              "--sidebar-width-icon": SIDEBAR_WIDTH_ICON,
              ...style,
            } as React.CSSProperties
          }
          className={cn(
            "group/sidebar-wrapper has-data-[variant=inset]:bg-sidebar flex min-h-svh w-full",
            className
          )}
          {...props}
        >
          {children}
        </div>
      </TooltipProvider>
    </SidebarContext.Provider>
  );
}

function Sidebar({
  side = "left",
  variant = "sidebar",
  collapsible = "offcanvas",
  className,
  children,
  ...props
}: React.ComponentProps<"div"> & {
  side?: "left" | "right";
  variant?: "sidebar" | "floating" | "inset";
  collapsible?: "offcanvas" | "icon" | "none";
}) {
  const { isMobile, state, openMobile, setOpenMobile } = useSidebar();

  if (collapsible === "none") {
    return (
      <div
        data-slot="sidebar"
        className={cn(
          "bg-sidebar text-sidebar-foreground flex h-full w-(--sidebar-width) flex-col",
          className
        )}
        {...props}
      >
        {children}
      </div>
    );
  }

  if (isMobile) {
    return (
      <Sheet open={openMobile} onOpenChange={setOpenMobile} {...props}>
        <SheetContent
          data-sidebar="sidebar"
          data-slot="sidebar"
          data-mobile="true"
          className="bg-sidebar text-sidebar-foreground w-(--sidebar-width) p-0 [&>button]:hidden"
          style={
            {
              "--sidebar-width": SIDEBAR_WIDTH_MOBILE,
            } as React.CSSProperties
          }
          side={side}
        >
          <SheetHeader className="sr-only">
            <SheetTitle>Sidebar</SheetTitle>
            <SheetDescription>Displays the mobile sidebar.</SheetDescription>
          </SheetHeader>
          <div className="flex h-full w-full flex-col">{children}</div>
        </SheetContent>
      </Sheet>
    );
  }

  return (
    <div
      className="group peer text-sidebar-foreground hidden md:block"
      data-state={state}
      data-collapsible={state === "collapsed" ? collapsible : ""}
      data-variant={variant}
      data-side={side}
      data-slot="sidebar"
    >
      {/* This is what handles the sidebar gap on desktop */}
      <div
        data-slot="sidebar-gap"
        className={cn(
          "relative w-(--sidebar-width) bg-transparent transition-[width] duration-200 ease-linear",
          "group-data-[collapsible=offcanvas]:w-0",
          "group-data-[side=right]:rotate-180",
          variant === "floating" || variant === "inset"
            ? "group-data-[collapsible=icon]:w-[calc(var(--sidebar-width-icon)+(--spacing(4)))]"
            : "group-data-[collapsible=icon]:w-(--sidebar-width-icon)"
        )}
      />
      <div
        data-slot="sidebar-container"
        className={cn(
          "fixed inset-y-0 z-10 hidden h-svh w-(--sidebar-width) transition-[left,right,width] duration-200 ease-linear md:flex",
          side === "left"
            ? "left-0 group-data-[collapsible=offcanvas]:left-[calc(var(--sidebar-width)*-1)]"
            : "right-0 group-data-[collapsible=offcanvas]:right-[calc(var(--sidebar-width)*-1)]",
          // Adjust the padding for floating and inset variants.
          variant === "floating" || variant === "inset"
            ? "p-2 group-data-[collapsible=icon]:w-[calc(var(--sidebar-width-icon)+(--spacing(4))+2px)]"
            : "group-data-[collapsible=icon]:w-(--sidebar-width-icon) group-data-[side=left]:border-none group-data-[side=right]:border-l",
          className
        )}
        {...props}
      >
        <div
          data-sidebar="sidebar"
          data-slot="sidebar-inner"
          className="bg-background/10  group-data-[variant=floating]:border-sidebar-border flex h-full w-full flex-col group-data-[variant=floating]:rounded-lg group-data-[variant=floating]:border group-data-[variant=floating]:shadow-sm"
        >
          {children}
        </div>
      </div>
    </div>
  );
}

function SidebarTrigger({
  className,
  onClick,
  ...props
}: React.ComponentProps<typeof Button>) {
  const { toggleSidebar } = useSidebar();

  return (
    <Button
      data-sidebar="trigger"
      data-slot="sidebar-trigger"
      variant="ghost"
      size="icon"
      className={cn("size-7", className)}
      onClick={(event) => {
        onClick?.(event);
        toggleSidebar();
      }}
      {...props}
    >
      <PanelLeftIcon />
      <span className="sr-only">Toggle Sidebar</span>
    </Button>
  );
}

function SidebarRail({ className, ...props }: React.ComponentProps<"button">) {
  const { toggleSidebar } = useSidebar();

  return (
    <button
      data-sidebar="rail"
      data-slot="sidebar-rail"
      aria-label="Toggle Sidebar"
      tabIndex={-1}
      onClick={toggleSidebar}
      title="Toggle Sidebar"
      className={cn(
        "hover:after:bg-sidebar-border absolute inset-y-0 z-20 hidden w-4 -translate-x-1/2 transition-all ease-linear group-data-[side=left]:-right-4 group-data-[side=right]:left-0 after:absolute after:inset-y-0 after:left-1/2 after:w-[2px] sm:flex",
        "in-data-[side=left]:cursor-w-resize in-data-[side=right]:cursor-e-resize",
        "[[data-side=left][data-state=collapsed]_&]:cursor-e-resize [[data-side=right][data-state=collapsed]_&]:cursor-w-resize",
        "hover:group-data-[collapsible=offcanvas]:bg-sidebar group-data-[collapsible=offcanvas]:translate-x-0 group-data-[collapsible=offcanvas]:after:left-full",
        "[[data-side=left][data-collapsible=offcanvas]_&]:-right-2",
        "[[data-side=right][data-collapsible=offcanvas]_&]:-left-2",
        className
      )}
      {...props}
    />
  );
}

function SidebarInset({ className, ...props }: React.ComponentProps<"main">) {
  return (
    <main
      data-slot="sidebar-inset"
      className={cn(
        "bg-background relative flex w-full flex-1 flex-col",
        "md:peer-data-[variant=inset]:m-2 md:peer-data-[variant=inset]:ml-0 md:peer-data-[variant=inset]:rounded-xl md:peer-data-[variant=inset]:shadow-sm md:peer-data-[variant=inset]:peer-data-[state=collapsed]:ml-2",
        className
      )}
      {...props}
    />
  );
}

function SidebarInput({
  className,
  ...props
}: React.ComponentProps<typeof Input>) {
  return (
    <Input
      data-slot="sidebar-input"
      data-sidebar="input"
      className={cn("bg-background h-8 w-full shadow-none", className)}
      {...props}
    />
  );
}

function SidebarHeader({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="sidebar-header"
      data-sidebar="header"
      className={cn("flex flex-col gap-2 p-2", className)}
      {...props}
    />
  );
}

function SidebarFooter({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="sidebar-footer"
      data-sidebar="footer"
      className={cn("flex flex-col gap-2 p-2", className)}
      {...props}
    />
  );
}

function SidebarSeparator({
  className,
  ...props
}: React.ComponentProps<typeof Separator>) {
  return (
    <Separator
      data-slot="sidebar-separator"
      data-sidebar="separator"
      className={cn("bg-sidebar-border mx-2 w-auto", className)}
      {...props}
    />
  );
}

function SidebarContent({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="sidebar-content"
      data-sidebar="content"
      className={cn(
        "flex min-h-0 flex-1 flex-col gap-2 overflow-auto group-data-[collapsible=icon]:overflow-hidden",
        className
      )}
      {...props}
    />
  );
}

function SidebarGroup({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="sidebar-group"
      data-sidebar="group"
      className={cn("relative flex w-full min-w-0 flex-col p-2", className)}
      {...props}
    />
  );
}

function SidebarGroupLabel({
  className,
  asChild = false,
  ...props
}: React.ComponentProps<"div"> & { asChild?: boolean }) {
  const Comp = asChild ? Slot : "div";

  return (
    <Comp
      data-slot="sidebar-group-label"
      data-sidebar="group-label"
      className={cn(
        "text-sidebar-foreground/70 ring-sidebar-ring flex h-8 shrink-0 items-center rounded-md px-2 text-xs font-medium outline-hidden transition-[margin,opacity] duration-200 ease-linear focus-visible:ring-2 [&>svg]:size-4 [&>svg]:shrink-0",
        "group-data-[collapsible=icon]:-mt-8 group-data-[collapsible=icon]:opacity-0",
        className
      )}
      {...props}
    />
  );
}

function SidebarGroupAction({
  className,
  asChild = false,
  ...props
}: React.ComponentProps<"button"> & { asChild?: boolean }) {
  const Comp = asChild ? Slot : "button";

  return (
    <Comp
      data-slot="sidebar-group-action"
      data-sidebar="group-action"
      className={cn(
        "text-sidebar-foreground ring-sidebar-ring hover:bg-sidebar-accent hover:text-sidebar-accent-foreground absolute top-3.5 right-3 flex aspect-square w-5 items-center justify-center rounded-md p-0 outline-hidden transition-transform focus-visible:ring-2 [&>svg]:size-4 [&>svg]:shrink-0",
        // Increases the hit area of the button on mobile.
        "after:absolute after:-inset-2 md:after:hidden",
        "group-data-[collapsible=icon]:hidden",
        className
      )}
      {...props}
    />
  );
}

function SidebarGroupContent({
  className,
  ...props
}: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="sidebar-group-content"
      data-sidebar="group-content"
      className={cn("w-full text-sm", className)}
      {...props}
    />
  );
}

function SidebarMenu({ className, ...props }: React.ComponentProps<"ul">) {
  return (
    <ul
      data-slot="sidebar-menu"
      data-sidebar="menu"
      className={cn("flex w-full min-w-0 flex-col gap-1", className)}
      {...props}
    />
  );
}

function SidebarMenuItem({ className, ...props }: React.ComponentProps<"li">) {
  return (
    <li
      data-slot="sidebar-menu-item"
      data-sidebar="menu-item"
      className={cn("group/menu-item relative", className)}
      {...props}
    />
  );
}

const sidebarMenuButtonVariants = cva(
  "peer/menu-button flex w-full items-center gap-2 overflow-hidden rounded-md p-2 text-left text-sm outline-hidden ring-sidebar-ring transition-[width,height,padding] hover:bg-sidebar-accent hover:text-sidebar-accent-foreground focus-visible:ring-2 active:bg-sidebar-accent active:text-sidebar-accent-foreground disabled:pointer-events-none disabled:opacity-50 group-has-data-[sidebar=menu-action]/menu-item:pr-8 aria-disabled:pointer-events-none aria-disabled:opacity-50 data-[active=true]:bg-sidebar-accent data-[active=true]:font-medium data-[active=true]:text-sidebar-accent-foreground data-[state=open]:hover:bg-sidebar-accent data-[state=open]:hover:text-sidebar-accent-foreground group-data-[collapsible=icon]:size-8! group-data-[collapsible=icon]:p-2! [&>span:last-child]:truncate [&>svg]:size-4 [&>svg]:shrink-0",
  {
    variants: {
      variant: {
        default: "hover:bg-sidebar-accent hover:text-sidebar-accent-foreground",
        outline:
          "bg-background shadow-[0_0_0_1px_hsl(var(--sidebar-border))] hover:bg-sidebar-accent hover:text-sidebar-accent-foreground hover:shadow-[0_0_0_1px_hsl(var(--sidebar-accent))]",
      },
      size: {
        default: "h-8 text-sm",
        sm: "h-7 text-xs",
        lg: "h-12 text-sm group-data-[collapsible=icon]:p-0!",
      },
    },
    defaultVariants: {
      variant: "default",
      size: "default",
    },
  }
);

function SidebarMenuButton({
  asChild = false,
  isActive = false,
  variant = "default",
  size = "default",
  tooltip,
  className,
  ...props
}: React.ComponentProps<"button"> & {
  asChild?: boolean;
  isActive?: boolean;
  tooltip?: string | React.ComponentProps<typeof TooltipContent>;
} & VariantProps<typeof sidebarMenuButtonVariants>) {
  const Comp = asChild ? Slot : "button";
  const { isMobile, state } = useSidebar();

  const button = (
    <Comp
      data-slot="sidebar-menu-button"
      data-sidebar="menu-button"
      data-size={size}
      data-active={isActive}
      className={cn(sidebarMenuButtonVariants({ variant, size }), className)}
      {...props}
    />
  );

  if (!tooltip) {
    return button;
  }

  if (typeof tooltip === "string") {
    tooltip = {
      children: tooltip,
    };
  }

  return (
    <Tooltip>
      <TooltipTrigger asChild>{button}</TooltipTrigger>
      <TooltipContent
        side="right"
        align="center"
        hidden={state !== "collapsed" || isMobile}
        {...tooltip}
      />
    </Tooltip>
  );
}

function SidebarMenuAction({
  className,
  asChild = false,
  showOnHover = false,
  ...props
}: React.ComponentProps<"button"> & {
  asChild?: boolean;
  showOnHover?: boolean;
}) {
  const Comp = asChild ? Slot : "button";

  return (
    <Comp
      data-slot="sidebar-menu-action"
      data-sidebar="menu-action"
      className={cn(
        "text-sidebar-foreground ring-sidebar-ring hover:bg-sidebar-accent hover:text-sidebar-accent-foreground peer-hover/menu-button:text-sidebar-accent-foreground absolute top-1.5 right-1 flex aspect-square w-5 items-center justify-center rounded-md p-0 outline-hidden transition-transform focus-visible:ring-2 [&>svg]:size-4 [&>svg]:shrink-0",
        // Increases the hit area of the button on mobile.
        "after:absolute after:-inset-2 md:after:hidden",
        "peer-data-[size=sm]/menu-button:top-1",
        "peer-data-[size=default]/menu-button:top-1.5",
        "peer-data-[size=lg]/menu-button:top-2.5",
        "group-data-[collapsible=icon]:hidden",
        showOnHover &&
        "peer-data-[active=true]/menu-button:text-sidebar-accent-foreground group-focus-within/menu-item:opacity-100 group-hover/menu-item:opacity-100 data-[state=open]:opacity-100 md:opacity-0",
        className
      )}
      {...props}
    />
  );
}

function SidebarMenuBadge({
  className,
  ...props
}: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="sidebar-menu-badge"
      data-sidebar="menu-badge"
      className={cn(
        "text-sidebar-foreground pointer-events-none absolute right-1 flex h-5 min-w-5 items-center justify-center rounded-md px-1 text-xs font-medium tabular-nums select-none",
        "peer-hover/menu-button:text-sidebar-accent-foreground peer-data-[active=true]/menu-button:text-sidebar-accent-foreground",
        "peer-data-[size=sm]/menu-button:top-1",
        "peer-data-[size=default]/menu-button:top-1.5",
        "peer-data-[size=lg]/menu-button:top-2.5",
        "group-data-[collapsible=icon]:hidden",
        className
      )}
      {...props}
    />
  );
}

function SidebarMenuSkeleton({
  className,
  showIcon = false,
  ...props
}: React.ComponentProps<"div"> & {
  showIcon?: boolean;
}) {
  // Random width between 50 to 90%.
  const [width, setWidth] = React.useState("50%");

  React.useEffect(() => {
    setWidth(`${Math.floor(Math.random() * 40) + 50}%`);
  }, []);

  return (
    <div
      data-slot="sidebar-menu-skeleton"
      data-sidebar="menu-skeleton"
      className={cn("flex h-8 items-center gap-2 rounded-md px-2", className)}
      {...props}
    >
      {showIcon && (
        <Skeleton
          className="size-4 rounded-md"
          data-sidebar="menu-skeleton-icon"
        />
      )}
      <Skeleton
        className="h-4 max-w-(--skeleton-width) flex-1"
        data-sidebar="menu-skeleton-text"
        style={
          {
            "--skeleton-width": width,
          } as React.CSSProperties
        }
      />
    </div>
  );
}

function SidebarMenuSub({ className, ...props }: React.ComponentProps<"ul">) {
  return (
    <ul
      data-slot="sidebar-menu-sub"
      data-sidebar="menu-sub"
      className={cn(
        "border-sidebar-border mx-3.5 flex min-w-0 translate-x-px flex-col gap-1 border-l px-2.5 py-0.5",
        "group-data-[collapsible=icon]:hidden",
        className
      )}
      {...props}
    />
  );
}

function SidebarMenuSubItem({
  className,
  ...props
}: React.ComponentProps<"li">) {
  return (
    <li
      data-slot="sidebar-menu-sub-item"
      data-sidebar="menu-sub-item"
      className={cn("group/menu-sub-item relative", className)}
      {...props}
    />
  );
}

function SidebarMenuSubButton({
  asChild = false,
  size = "md",
  isActive = false,
  className,
  ...props
}: React.ComponentProps<"a"> & {
  asChild?: boolean;
  size?: "sm" | "md";
  isActive?: boolean;
}) {
  const Comp = asChild ? Slot : "a";

  return (
    <Comp
      data-slot="sidebar-menu-sub-button"
      data-sidebar="menu-sub-button"
      data-size={size}
      data-active={isActive}
      className={cn(
        "text-sidebar-foreground ring-sidebar-ring hover:bg-sidebar-accent hover:text-sidebar-accent-foreground active:bg-sidebar-accent active:text-sidebar-accent-foreground [&>svg]:text-sidebar-accent-foreground flex h-7 min-w-0 -translate-x-px items-center gap-2 overflow-hidden rounded-md px-2 outline-hidden focus-visible:ring-2 disabled:pointer-events-none disabled:opacity-50 aria-disabled:pointer-events-none aria-disabled:opacity-50 [&>span:last-child]:truncate [&>svg]:size-4 [&>svg]:shrink-0",
        "data-[active=true]:bg-sidebar-accent data-[active=true]:text-sidebar-accent-foreground",
        size === "sm" && "text-xs",
        size === "md" && "text-sm",
        "group-data-[collapsible=icon]:hidden",
        className
      )}
      {...props}
    />
  );
}

export {
  Sidebar,
  SidebarContent,
  SidebarFooter,
  SidebarGroup,
  SidebarGroupAction,
  SidebarGroupContent,
  SidebarGroupLabel,
  SidebarHeader,
  SidebarInput,
  SidebarInset,
  SidebarMenu,
  SidebarMenuAction,
  SidebarMenuBadge,
  SidebarMenuButton,
  SidebarMenuItem,
  SidebarMenuSkeleton,
  SidebarMenuSub,
  SidebarMenuSubButton,
  SidebarMenuSubItem,
  SidebarProvider,
  SidebarRail,
  SidebarSeparator,
  SidebarTrigger,
  useSidebar,
};

```

---

## `src\components\ui\skeleton.tsx`

```tsx
import { cn } from "@/lib/utils"

function Skeleton({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="skeleton"
      className={cn("bg-accent animate-pulse rounded-md", className)}
      {...props}
    />
  )
}

export { Skeleton }

```

---

## `src\components\ui\slider.tsx`

```tsx
"use client"

import * as React from "react"
import * as SliderPrimitive from "@radix-ui/react-slider"

import { cn } from "@/lib/utils"

function Slider({
  className,
  defaultValue,
  value,
  min = 0,
  max = 100,
  ...props
}: React.ComponentProps<typeof SliderPrimitive.Root>) {
  const _values = React.useMemo(
    () =>
      Array.isArray(value)
        ? value
        : Array.isArray(defaultValue)
          ? defaultValue
          : [min, max],
    [value, defaultValue, min, max]
  )

  return (
    <SliderPrimitive.Root
      data-slot="slider"
      defaultValue={defaultValue}
      value={value}
      min={min}
      max={max}
      className={cn(
        "relative flex w-full touch-none items-center select-none data-[disabled]:opacity-50 data-[orientation=vertical]:h-full data-[orientation=vertical]:min-h-44 data-[orientation=vertical]:w-auto data-[orientation=vertical]:flex-col",
        className
      )}
      {...props}
    >
      <SliderPrimitive.Track
        data-slot="slider-track"
        className={cn(
          "bg-muted relative grow overflow-hidden rounded-full data-[orientation=horizontal]:h-1.5 data-[orientation=horizontal]:w-full data-[orientation=vertical]:h-full data-[orientation=vertical]:w-1.5"
        )}
      >
        <SliderPrimitive.Range
          data-slot="slider-range"
          className={cn(
            "bg-primary absolute data-[orientation=horizontal]:h-full data-[orientation=vertical]:w-full"
          )}
        />
      </SliderPrimitive.Track>
      {Array.from({ length: _values.length }, (_, index) => (
        <SliderPrimitive.Thumb
          data-slot="slider-thumb"
          key={index}
          className="border-primary ring-ring/50 block size-4 shrink-0 rounded-full border bg-white shadow-sm transition-[color,box-shadow] hover:ring-4 focus-visible:ring-4 focus-visible:outline-hidden disabled:pointer-events-none disabled:opacity-50"
        />
      ))}
    </SliderPrimitive.Root>
  )
}

export { Slider }

```

---

## `src\components\ui\smooth-scroll.tsx`

```tsx
'use client'
import { ReactNode, useEffect } from 'react'
import Lenis from 'lenis'

export interface SmoothScrollProps {
    children: ReactNode
}

export function SmoothScroll({ children }: SmoothScrollProps) {
    useEffect(() => {
        const lenis = new Lenis()

        function raf(time: number) {
            lenis.raf(time)
            requestAnimationFrame(raf)
        }

        requestAnimationFrame(raf)

        // Remove loading class
        document.body.classList.remove('loading');

        return () => {
            lenis.destroy()
        }
    }, [])

    return <>{children}</>
}

export default SmoothScroll

```

---

## `src\components\ui\social-flip-button.tsx`

```tsx
"use client";

import { motion, AnimatePresence } from "framer-motion";
import React, { useState } from "react";
import { cn } from "@/lib/utils";
import {
    FaGithub,
    FaTwitter,
    FaFacebook,
    FaInstagram,
    FaLinkedin,
    FaEnvelope,
    FaDiscord,
} from "react-icons/fa";

export interface SocialItem {
    letter: string;
    icon: React.ReactNode;
    label: string;
    href?: string;
    onClick?: () => void;
}

interface SocialFlipButtonProps {
    items?: SocialItem[];
    className?: string;
    itemClassName?: string;
    frontClassName?: string;
    backClassName?: string;
}

const defaultItems: SocialItem[] = [
    { letter: "C", icon: <FaGithub />, label: "Github", href: "#" },
    { letter: "O", icon: <FaTwitter />, label: "Twitter", href: "#" },
    { letter: "N", icon: <FaLinkedin />, label: "LinkedIn", href: "#" },
    { letter: "T", icon: <FaInstagram />, label: "Instagram", href: "#" },
    { letter: "A", icon: <FaFacebook />, label: "Facebook", href: "#" },
    { letter: "C", icon: <FaEnvelope />, label: "Email", href: "#" },
    { letter: "T", icon: <FaDiscord />, label: "Discord", href: "#" },
];

const SocialFlipNode = ({
    item,
    index,
    isHovered,
    setTooltipIndex,
    tooltipIndex,
    itemClassName,
    frontClassName,
    backClassName,
}: {
    item: SocialItem;
    index: number;
    isHovered: boolean;
    setTooltipIndex: (val: number | null) => void;
    tooltipIndex: number | null;
    itemClassName?: string;
    frontClassName?: string;
    backClassName?: string;
}) => {
    const Wrapper = item.href ? "a" : "div";
    const wrapperProps = item.href
        ? { href: item.href, target: "_blank", rel: "noopener noreferrer" }
        : { onClick: item.onClick };

    return (
        <Wrapper
            {...wrapperProps}
            className={cn("relative h-10 w-10 cursor-pointer", itemClassName)}
            style={{ perspective: "1000px" }}
            onMouseEnter={() => setTooltipIndex(index)}
            onMouseLeave={() => setTooltipIndex(null)}
        >
            <AnimatePresence>
                {isHovered && tooltipIndex === index && (
                    <motion.div
                        initial={{ opacity: 0, y: 10, scale: 0.8, x: "-50%" }}
                        animate={{ opacity: 1, y: -50, scale: 1, x: "-50%" }}
                        exit={{ opacity: 0, y: 10, scale: 0.8, x: "-50%" }}
                        transition={{ duration: 0.2, ease: "easeOut" }}
                        className="absolute left-1/2 z-50 whitespace-nowrap rounded-lg bg-neutral-900 px-3 py-1.5 text-xs font-semibold text-white shadow-xl dark:bg-white dark:text-neutral-900"
                    >
                        {item.label}
                        {/* Arrow */}
                        <div className="absolute -bottom-1 left-1/2 -translate-x-1/2 h-2 w-2 rotate-45 bg-neutral-900 dark:bg-white" />
                    </motion.div>
                )}
            </AnimatePresence>

            <motion.div
                className="relative h-full w-full"
                initial={false}
                animate={{ rotateY: isHovered ? 180 : 0 }}
                transition={{
                    duration: 0.8,
                    type: "spring",
                    stiffness: 120,
                    damping: 15,
                    delay: index * 0.08,
                }}
                style={{ transformStyle: "preserve-3d" }}
            >
                {/* Front - Letter */}
                <div
                    className={cn(
                        "absolute inset-0 flex items-center justify-center rounded-lg bg-neutral-100 text-lg font-bold text-neutral-800 shadow-sm dark:bg-neutral-900 dark:text-neutral-200",
                        frontClassName
                    )}
                    style={{ backfaceVisibility: "hidden" }}
                    
                >
                    {item.letter}
                </div>

                {/* Back - Icon */}
                <div
                    className={cn(
                        "absolute inset-0 flex items-center justify-center rounded-lg bg-black text-lg text-white dark:bg-white dark:text-black",
                        backClassName
                    )}
                    style={{
                        backfaceVisibility: "hidden",
                        transform: "rotateY(180deg)",
                    }}
                >
                    {item.icon}
                </div>
            </motion.div>
        </Wrapper>
    );
};

export default function SocialFlipButton({
    items = defaultItems,
    className,
    itemClassName,
    frontClassName,
    backClassName,
}: SocialFlipButtonProps) {
    const [isHovered, setIsHovered] = useState(false);
    const [tooltipIndex, setTooltipIndex] = useState<number | null>(null);
    const [isDark, setIsDark] = useState(false);

    React.useEffect(() => {
        const checkTheme = () => {
            setIsDark(document.documentElement.classList.contains('dark'));
        };
        checkTheme();
        const observer = new MutationObserver(checkTheme);
        observer.observe(document.documentElement, { attributes: true, attributeFilter: ['class'] });
        return () => observer.disconnect();
    }, []);

    return (
        <div className={cn("flex items-center justify-center gap-4 p-4", className)}>
            <div
                className="group relative flex items-center justify-center gap-2 rounded-2xl glass-border bg-white p-4 shadow-sm dark:bg-black"
                onMouseEnter={() => setIsHovered(true)}
                onMouseLeave={() => {
                    setIsHovered(false);
                    setTooltipIndex(null);
                }}
            >
                {/* Border Lines Container - Clipped */}
                <div className="absolute -inset-[1px] overflow-hidden rounded-2xl pointer-events-none">
                    {/* Animated Top Border Line */}
                    <motion.div
                        className="absolute top-0 left-0 h-[1px] w-full bg-gradient-to-r from-transparent via-black/50 to-transparent dark:via-white/50"
                        animate={{ x: ["-100%", "100%"] }}
                        transition={{
                            duration: 2.5,
                            repeat: Infinity,
                            ease: "linear",
                        }}
                    />

                    {/* Animated Bottom Border Line */}
                    <motion.div
                        className="absolute bottom-0 left-0 h-[1px] w-full bg-gradient-to-r from-transparent via-black/50 to-transparent dark:via-white/50"
                        animate={{ x: ["100%", "-100%"] }}
                        transition={{
                            duration: 2.5,
                            repeat: Infinity,
                            ease: "linear",
                        }}
                    />
                </div>

                {items.map((item, index) => (
                    <SocialFlipNode
                        key={index}
                        item={item}
                        index={index}
                        isHovered={isHovered}
                        setTooltipIndex={setTooltipIndex}
                        tooltipIndex={tooltipIndex}
                        itemClassName={itemClassName}
                        frontClassName={frontClassName}
                        backClassName={backClassName}
                    />
                ))}
            </div>
        </div>
    );
}

```

---

## `src\components\ui\solar-system.tsx`

```tsx
'use client';

import React, { useState } from "react";
import { Orbit as OrbitIcon } from "lucide-react";
import { cn } from "@/lib/utils";

/**
 * ============================================================================
 * TYPE DEFINITIONS & CUSTOMIZATION CONTRACTS
 * ============================================================================
 */

// Represents an individual planet/technology node orbiting the core.
export interface SolarSystemItem {
  id: string;      // Unique identifier (e.g. "react", "rust")
  label: string;   // Text label shown next to the icon (e.g. "React")
  type?: string;   // Optional description tag (e.g. "Frontend Framework")
  badge?: string;  // Optional status badge text (e.g. "Official SDK")
  desc?: string;   // Optional longer detail description
  color: string;   // HEX or CSS color used for active glowing highlights (e.g. "#61DAFB")
  svg: React.ReactNode; // React node / Inline SVG rendering the item's logo icon
}

// Configures a single orbit ring layer (e.g. inner, middle, outer).
export interface OrbitConfig {
  id: string;          // Unique ring identifier (e.g. "inner")
  name: string;        // Readable name for the ring
  radiusClass: string; // CSS variable or absolute size (e.g. "var(--radius-inner)")
  radiusPx: number;    // Absolute size in pixels (used for cosmic dust calculations)
  speed: number;       // Base duration in seconds for one full 360-degree rotation
  items: SolarSystemItem[]; // Array of planet items revolving on this ring
}

export interface SolarSystemProps extends React.HTMLAttributes<HTMLDivElement> {
  // Pass a custom URL string or React element to override the default center sun core logo.
  centerLogo?: string | React.ReactNode;
  centerLogoAlt?: string; // Accessibility description for the center logo
  orbits?: OrbitConfig[]; // Custom array of rings and planets (defaults to DEFAULT_ORBITS)
  isPaused?: boolean;     // Stop all rotation animations (play/pause control)
  speedMultiplier?: number; // Speed multiplier for faster/slower rotations (default: 1)
}

/**
 * ============================================================================
 * DEFAULT SVG LOGO ICONS
 * Edit these SVG definitions to change the default icons shown on planets.
 * ============================================================================
 */
const DefaultIcons = {
  react: (
    <svg viewBox="-11.5 -10.23174 23 20.46348" className="w-5 h-5" fill="none">
      <circle cx="0" cy="0" r="2.05" fill="#61DAFB" />
      <g stroke="#61DAFB" strokeWidth="1">
        <ellipse rx="11" ry="4.2" />
        <ellipse rx="11" ry="4.2" transform="rotate(60)" />
        <ellipse rx="11" ry="4.2" transform="rotate(120)" />
      </g>
    </svg>
  ),
  nextjs: (
    <svg viewBox="0 0 180 180" className="w-5 h-5" fill="none">
      <circle cx="90" cy="90" r="90" fill="#000" stroke="#fff" strokeWidth="6" />
      <path
        d="M149.508 157.52L69.142 54H54v72h14.4V69.412l67.24 87.054a89.4 89.4 0 0013.868-1.046zM111.6 54h14.4v72h-14.4z"
        fill="#fff"
      />
    </svg>
  ),
  flutter: (
    <svg viewBox="0 0 24 24" className="w-5 h-5" fill="none">
      <path d="M14.314 0L2.3 12l6 6 6-6-6-6 2.985-2.985L14.314 0zm0 6l-6 6 6 6 5.7-5.7-2.985-3L20.014 6H14.314z" fill="#54C5F8" />
      <path d="M14.314 12L8.3 18l6 6h5.7l-6-6 6-6h-5.7z" fill="#01579B" />
    </svg>
  ),
  vue: (
    <svg viewBox="0 0 256 221" className="w-5 h-5" fill="none">
      <path d="M204.8 0H256L128 220.8 0 0h51.2L128 132.48 204.8 0z" fill="#41B883" />
      <path d="M0 0l128 220.8L256 0h-51.2L128 132.48 51.2 0H0z" fill="#35495E" />
    </svg>
  ),
  typescript: (
    <svg viewBox="0 0 24 24" className="w-5 h-5" fill="none">
      <rect width="24" height="24" rx="2" fill="#3178C6" />
      <path d="M5.5 12v-1.5h4.5V21h-2V12H5.5zm5.5-1.5h4.5v1.5h-3v2h3v1.5h-3v2h3V21h-4.5v-1.5h3v-2h-3v-1.5h3v-2h-3V10.5z" fill="#fff" />
    </svg>
  ),
  python: (
    <svg viewBox="0 0 24 24" className="w-5 h-5" fill="none">
      <path
        d="M12.043 1.017c-2.157 0-2.078.918-2.078.918l.003 2.126h2.32v.639H7.768S5.449 4.487 5.449 6.72c0 2.233 0 2.767 0 2.767h1.568V8.282c0-.725.68-1.324 1.582-1.324h3.722c1.383 0 2.234-.84 2.234-2.234V3.11c0-1.156-.99-2.093-2.234-2.093h-2.32V1.017zm-1.09.934a.6.6 0 1 1 .002 1.2.6.6 0 0 1-.002-1.2z"
        fill="#387EB8"
      />
      <path
        d="M12.043 22.983c2.157 0 2.078-.918 2.078-.918l-.003-2.126h-2.32v-.639h4.518s2.32.217 2.32-2.017c0-2.233 0-2.767 0-2.767h-1.568v1.201c0 .725-.68 1.324-1.582 1.324h-3.722c-1.383 0-2.234.84-2.234 2.234v1.867c0 1.156.99 2.093 2.234 2.093h2.32v-.252h-.041z"
        fill="#FFD43B"
      />
    </svg>
  ),
  rust: (
    <svg viewBox="0 0 24 24" className="w-5 h-5" fill="none">
      <circle cx="12" cy="12" r="10" stroke="#ff6f30" strokeWidth="2" strokeDasharray="4 3" />
      <path d="M12 6a6 6 0 1 0 0 12 6 6 0 0 0 0-12zm0 2a4 4 0 1 1 0 8 4 4 0 0 1 0-8z" fill="#ff6f30" />
      <circle cx="12" cy="12" r="1.5" fill="#fff" />
    </svg>
  ),
  golang: (
    <svg viewBox="0 0 24 24" className="w-5 h-5" fill="none">
      <path
        d="M3.8 8.15a.36.36 0 0 0-.36.41.36.36 0 0 0 .36.33h4.69a.36.36 0 0 0 .36-.33.36.36 0 0 0-.36-.41H3.8zm-2.56 1.8a.36.36 0 0 0-.36.45.36.36 0 0 0 .36.33h4.69a.36.36 0 0 0 .36-.33.36.36 0 0 0-.36-.45H1.24zm13.8 0a.36.36 0 0 0-.36.45.36.36 0 0 0 .36.33h4.69a.36.36 0 0 0 .36-.33.36.36 0 0 0-.36-.45h-4.69zm-2.56-1.8a.36.36 0 0 0-.36.41.36.36 0 0 0 .36.33h4.69a.36.36 0 0 0 .36-.33.36.36 0 0 0-.36-.41h-4.69z"
        fill="#00ADD8"
      />
      <path
        d="M22.83 11.1c-.43-1.27-1.53-2.03-2.96-2.03-1.28 0-2.27.49-3.22 1.33.4.35.77.79 1.08 1.3.48-.5 1.06-.96 1.9-.96.84 0 1.4.47 1.57 1.17.03.1.04.2.04.32v.27c-.54-.02-1.18-.05-1.83-.05-2.16 0-3.35.5-4.16 1.5-.78.96-.9 2.14-.9 3.08 0 1.34.5 2.55 1.8 2.55.84 0 1.43-.37 1.91-.86.35.55.72 1.01 1.3 1.31.62.32 1.28.38 2.13.38 1.99 0 3.2-.95 3.85-2.6.54-1.34.38-2.5.03-3.28h-2.73v.02zm-1.16 3.73c-.48.75-1.12 1.06-1.83 1.06-.36 0-.72-.1-1-.32-.3-.23-.5-.56-.5-1.1 0-.8.36-1.42 1.06-1.8.56-.3 1.3-.42 2.23-.42.17 0 .35 0 .54.01-.03.72-.23 1.63-.5 2.57zM8.3 9.02H4.76c-.88 0-1.6.72-1.6 1.6v2.85c0 .88.72 1.6 1.6 1.6H8.3c1.52 0 3.04-1.36 3.04-3.03 0-1.66-1.52-3.02-3.04-3.02zm0 3.95H5.43v-.7H8.3s.36.1.36.43c0 .34-.36.26-.36.26v.01z"
        fill="#00ADD8"
      />
    </svg>
  ),
};

/**
 * ============================================================================
 * DEFAULT ORBITS CONFIGURATION
 * Customizing these arrays is the easiest way to add/remove rings or change
 * which planets revolve on each orbit, along with their colors and speeds.
 * ============================================================================
 */
const DEFAULT_ORBITS: OrbitConfig[] = [
  {
    id: "inner",
    name: "Inner Ring",
    radiusClass: "var(--radius-inner)", // Maps to CSS radius variables defined below
    radiusPx: 175,
    speed: 20,                          // Time in seconds to complete a full orbit
    items: [
      {
        id: "react",
        label: "React",
        color: "#61DAFB",
        svg: DefaultIcons.react,
      },
      {
        id: "nextjs",
        label: "Next.js",
        color: "#ffffff",
        svg: DefaultIcons.nextjs,
      },
      {
        id: "flutter",
        label: "Flutter",
        color: "#02569B",
        svg: DefaultIcons.flutter,
      },
      {
        id: "vue",
        label: "Vue.js",
        color: "#42B883",
        svg: DefaultIcons.vue,
      },
    ],
  },
  {
    id: "mid",
    name: "Middle Ring",
    radiusClass: "var(--radius-mid)",
    radiusPx: 285,
    speed: 32,
    items: [
      {
        id: "typescript",
        label: "TypeScript",
        color: "#3178C6",
        svg: DefaultIcons.typescript,
      },
      {
        id: "python",
        label: "Python",
        color: "#FFD43B",
        svg: DefaultIcons.python,
      },
    ],
  },
  {
    id: "outer",
    name: "Outer Ring",
    radiusClass: "var(--radius-outer)",
    radiusPx: 395,
    speed: 48,
    items: [
      {
        id: "rust",
        label: "Rust",
        color: "#FF6F30",
        svg: DefaultIcons.rust,
      },
      {
        id: "golang",
        label: "Go",
        color: "#00ADD8",
        svg: DefaultIcons.golang,
      },
    ],
  },
];

export const SolarSystem = React.forwardRef<HTMLDivElement, SolarSystemProps>(
  (
    {
      centerLogo,             // Override central sun core with image path or custom React node
      centerLogoAlt = "Core Engine",
      orbits = DEFAULT_ORBITS, // Custom orbit rings list passed down via props
      isPaused = false,        // Control animation playback status dynamically
      speedMultiplier = 1,    // Factor to accelerate/decelerate orbits
      className,
      ...props
    },
    ref
  ) => {
    const [hoveredId, setHoveredId] = useState<string | null>(null);

    // Cosmic dust particle animations coordinates
    const dustItems = [
      { delay: "-4s", radius: "165px", color: "#00f5d4" },
      { delay: "-11s", radius: "260px", color: "#a855f7" },
      { delay: "-19s", radius: "340px", color: "#3b82f6" },
      { delay: "-28s", radius: "395px", color: "#00f5d4" },
      { delay: "-7s", radius: "200px", color: "#ec4899" },
      { delay: "-15s", radius: "365px", color: "#eab308" },
      { delay: "-23s", radius: "430px", color: "#a855f7" },
    ];

    return (
      <div
        ref={ref}
        className={cn(
          "relative flex items-center justify-center w-full max-w-[940px] h-[320px] md:h-[450px] perspective-[1200px] select-none overflow-visible",
          className
        )}
        {...props}
      >
        {/* 
          ======================================================================
          DYNAMIC CSS KEYFRAMES INJECTOR
          This guarantees the component is fully self-contained without needing 
          modifications in global project stylesheets.
          ======================================================================
        */}
        <style dangerouslySetInnerHTML={{ __html: `
          /* CUSTOMIZE ORBIT DIAMETER SIZES HERE */
          :root {
            --radius-inner: 175px;
            --radius-mid: 285px;
            --radius-outer: 395px;
          }

          /* Tablet Responsive Adjustments */
          @media (max-width: 768px) {
            :root {
              --radius-inner: 100px;
              --radius-mid: 165px;
              --radius-outer: 230px;
            }
          }

          /* Mobile Responsive Adjustments */
          @media (max-width: 480px) {
            :root {
              --radius-inner: 70px;
              --radius-mid: 115px;
              --radius-outer: 160px;
            }
          }

          /* Orbit revolutions */
          @keyframes custom-orbitMove {
            0% {
              transform: translate(-50%, -50%) rotateZ(0deg) translateX(var(--orbit-radius));
            }
            100% {
              transform: translate(-50%, -50%) rotateZ(-360deg) translateX(var(--orbit-radius));
            }
          }

          /* Billboard counter-rotation (cancels the 65deg X-tilt and 10deg Y-tilt) */
          @keyframes custom-billboardCancel {
            0% {
              transform: translate(-50%, -50%) rotateZ(0deg) rotateY(10deg) rotateX(-65deg);
            }
            100% {
              transform: translate(-50%, -50%) rotateZ(360deg) rotateY(10deg) rotateX(-65deg);
            }
          }

          /* Sun glow pulsations */
          @keyframes custom-sun-pulse {
            0% { transform: scale(0.9); opacity: 0.7; }
            100% { transform: scale(1.1); opacity: 1; }
          }

          /* Sun ring accessory speeds */
          @keyframes custom-spin-clockwise {
            0% { transform: rotateX(65deg) rotateY(-10deg) rotateZ(0deg); }
            100% { transform: rotateX(65deg) rotateY(-10deg) rotateZ(360deg); }
          }
          @keyframes custom-spin-counter {
            0% { transform: rotateX(65deg) rotateY(-10deg) rotateZ(0deg); }
            100% { transform: rotateX(65deg) rotateY(-10deg) rotateZ(-360deg); }
          }

          .animate-custom-orbit {
            animation: custom-orbitMove var(--orbit-duration) linear infinite;
            animation-play-state: var(--orbit-play-state);
          }
          .animate-custom-billboard {
            animation: custom-billboardCancel var(--orbit-duration) linear infinite;
            animation-play-state: var(--orbit-play-state);
          }
          .animate-custom-sun-pulse {
            animation: custom-sun-pulse 4s ease-in-out infinite alternate;
          }
          .animate-custom-spin-cw {
            animation: custom-spin-clockwise 20s linear infinite;
          }
          .animate-custom-spin-ccw {
            animation: custom-spin-counter 30s linear infinite;
          }

          /* Planet logo cards base styles */
          .orbit-logo-card {
            position: absolute;
            left: 50%;
            top: 50%;
            display: flex;
            align-items: center;
            gap: 8px;
            padding: 0.45rem 0.95rem;
            background: rgba(10, 10, 12, 0.65);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.08);
            border-radius: 100px;
            font-weight: 600;
            color: #ffffff;
            white-space: nowrap;
            user-select: none;
            cursor: pointer;
            pointer-events: auto;
            /* Transition scale independently to prevent transform overriding conflicts */
            transition: border-color 0.3s, color 0.3s, background 0.3s, box-shadow 0.3s, scale 0.3s;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.4), inset 0 1px 0 rgba(255, 255, 255, 0.05);
          }
        `}} />

        {/* Tiltable Orbit Container (handles 3D tilt coordinates) */}
        <div 
          className="absolute w-[360px] h-[360px] md:w-[940px] md:h-[940px] flex items-center justify-center"
          style={{
            transform: "rotateX(65deg) rotateY(-10deg)",
            transformStyle: "preserve-3d",
          }}
        >
          {/* 
            ======================================================================
            CENTRAL SUN CORE ENGINE
            Customized via "centerLogo" prop. Defaults to lucide Orbit icon.
            ======================================================================
          */}
          <div 
            className="absolute w-[100px] h-[100px] md:w-[130px] md:h-[130px] flex items-center justify-center z-20 pointer-events-none"
            style={{
              transform: "rotateY(10deg) rotateX(-65deg)",
              transformStyle: "preserve-3d",
            }}
          >
            {/* Glowing aura */}
            <div className="absolute w-[90px] h-[90px] md:w-[120px] md:h-[120px] rounded-full filter blur-md animate-custom-sun-pulse z-10 bg-teal-500/20" />
            
            {/* Sun Core Logo Render */}
            {centerLogo ? (
              typeof centerLogo === "string" ? (
                <img
                  className="w-14 h-14 md:w-20 md:h-20 rounded-full border-2 border-teal-500/40 shadow-[0_0_30px_rgba(20,184,166,0.3)] z-20 bg-zinc-950 p-2 md:p-3 relative"
                  src={centerLogo}
                  alt={centerLogoAlt}
                  width={80}
                  height={80}
                />
              ) : (
                <div className="w-14 h-14 md:w-20 md:h-20 rounded-full border-2 border-teal-500/40 shadow-[0_0_30px_rgba(20,184,166,0.3)] z-20 bg-zinc-950 flex items-center justify-center p-2 relative">
                  {centerLogo}
                </div>
              )
            ) : (
              <div className="w-14 h-14 md:w-20 md:h-20 rounded-full border-2 border-teal-500/40 shadow-[0_0_30px_rgba(20,184,166,0.3)] z-20 bg-zinc-950 flex items-center justify-center p-2 relative">
                <OrbitIcon className="w-8 h-8 text-teal-400 animate-spin" style={{ animationDuration: '10s' }} />
              </div>
            )}

            {/* Sun core dash rings */}
            <div className="absolute w-[110px] h-[110px] md:w-[140px] md:h-[140px] rounded-full border border-dashed border-teal-500/20 animate-custom-spin-cw pointer-events-none" />
            <div className="absolute w-[150px] h-[150px] md:w-[185px] md:h-[185px] rounded-full border border-dashed border-teal-500/10 animate-custom-spin-ccw pointer-events-none" />
          </div>

          {/* Cosmic Dust Particles */}
          {dustItems.map((dust, idx) => (
            <div
              key={idx}
              className="absolute left-1/2 top-1/2 w-1 h-1 rounded-full opacity-40 pointer-events-none animate-custom-orbit"
              style={{
                background: dust.color,
                boxShadow: `0 0 6px ${dust.color}`,
                animationDelay: dust.delay,
                animationPlayState: isPaused ? "paused" : "running",
                animationDuration: `${24 / speedMultiplier}s`,
                ["--orbit-radius" as any]: dust.radius,
                ["--orbit-duration" as any]: `${24 / speedMultiplier}s`,
                ["--orbit-play-state" as any]: isPaused ? "paused" : "running",
              }}
            />
          ))}

          {/* 
            ======================================================================
            ORBITS AND PLANET NODES RENDERING
            Loops through custom rings configuration to build the structure.
            ======================================================================
          */}
          {orbits.map((orbit) => {
            return (
              <React.Fragment key={orbit.id}>
                {/* Visual Dashed Ring Line representing this orbit level */}
                <div
                  className="absolute rounded-full border border-dashed border-zinc-700/60 pointer-events-none"
                  style={{
                    width: `calc(2 * ${orbit.radiusClass})`,
                    height: `calc(2 * ${orbit.radiusClass})`,
                    boxShadow: "inset 0 0 25px rgba(255, 255, 255, 0.01), 0 0 25px rgba(255, 255, 255, 0.01)",
                    ["--orbit-radius" as any]: orbit.radiusClass,
                  }}
                />

                {/* Orbit Items / Planet Cards */}
                {orbit.items.map((item, idx, arr) => {
                  // Distribute nodes evenly along the orbit circumference
                  const delayValue = -(orbit.speed / arr.length) * idx;
                  const durationValue = orbit.speed / speedMultiplier;
                  const isHovered = hoveredId === item.id;

                  return (
                    <div
                      key={item.id}
                      className="absolute left-1/2 top-1/2 w-0 h-0 pointer-events-none animate-custom-orbit"
                      style={{
                        animationDelay: `${delayValue}s`,
                        animationDuration: `${durationValue}s`,
                        animationPlayState: isPaused ? "paused" : "running",
                        ["--orbit-radius" as any]: orbit.radiusClass,
                        ["--orbit-duration" as any]: `${durationValue}s`,
                        ["--orbit-play-state" as any]: isPaused ? "paused" : "running",
                        ["--hover-color" as any]: item.color,
                        zIndex: isHovered ? 30 : 10,
                        transformStyle: "preserve-3d", // Crucial: preserves 3D context so children can billboard cancel correctly
                      }}
                    >
                      {/* Laser beam connecting sun center with planet (activates on hover) */}
                      <div
                        className="absolute right-0 top-1/2 h-[1.5px] origin-right -translate-y-1/2 pointer-events-none transition-opacity duration-300 z-0"
                        style={{
                          width: orbit.radiusClass,
                          opacity: isHovered ? 1 : 0,
                          background: `linear-gradient(90deg, rgba(0,0,0,0) 0%, rgba(255,255,255,0.15) 20%, ${item.color} 80%, ${item.color} 100%)`,
                          boxShadow: `0 0 8px ${item.color}, 0 0 16px ${item.color}40`,
                        }}
                      />

                      {/* 
                        ======================================================================
                        PLANET LOGO CARD
                        Contains billboardCancel keyframe animation to stay facing the camera.
                        ======================================================================
                      */}
                      <div
                        onMouseEnter={() => setHoveredId(item.id)}
                        onMouseLeave={() => setHoveredId(null)}
                        className="orbit-logo-card animate-custom-billboard"
                        style={{
                          animationDelay: `${delayValue}s`,
                          animationDuration: `${durationValue}s`,
                          animationPlayState: isPaused ? "paused" : "running",
                          borderColor: isHovered ? item.color : undefined,
                          boxShadow: isHovered 
                            ? `0 0 20px rgba(0, 0, 0, 0.6), 0 0 15px ${item.color}35`
                            : undefined,
                          scale: isHovered ? 1.05 : 1, // Scaled on hover independently to prevent transform conflicts
                          ["--orbit-duration" as any]: `${durationValue}s`,
                          ["--orbit-play-state" as any]: isPaused ? "paused" : "running",
                        }}
                      >
                        {/* Icon Container */}
                        <div 
                          className="transition-transform duration-300"
                          style={{
                            transform: isHovered ? "scale(1.1)" : "scale(1)",
                            color: item.color,
                          }}
                        >
                          {item.svg}
                        </div>
                        {/* Label Text */}
                        <span className="text-[11px] md:text-[13px] tracking-tight">{item.label}</span>
                      </div>
                    </div>
                  );
                })}
              </React.Fragment>
            );
          })}
        </div>
      </div>
    );
  }
);

SolarSystem.displayName = "SolarSystem";

```

---

## `src\components\ui\sonner.tsx`

```tsx
"use client"

import {
  CircleCheckIcon,
  InfoIcon,
  Loader2Icon,
  OctagonXIcon,
  TriangleAlertIcon,
} from "lucide-react"
import { useTheme } from "next-themes"
import { Toaster as Sonner, type ToasterProps } from "sonner"

const Toaster = ({ ...props }: ToasterProps) => {
  const { theme = "system" } = useTheme()

  return (
    <Sonner
      theme={theme as ToasterProps["theme"]}
      className="toaster group"
      icons={{
        success: <CircleCheckIcon className="size-4" />,
        info: <InfoIcon className="size-4" />,
        warning: <TriangleAlertIcon className="size-4" />,
        error: <OctagonXIcon className="size-4" />,
        loading: <Loader2Icon className="size-4 animate-spin" />,
      }}
      style={
        {
          "--normal-bg": "var(--popover)",
          "--normal-text": "var(--popover-foreground)",
          "--normal-border": "var(--border)",
          "--border-radius": "var(--radius)",
        } as React.CSSProperties
      }
      {...props}
    />
  )
}

export { Toaster }

```

---

## `src\components\ui\spinner.tsx`

```tsx
import { Loader2Icon } from "lucide-react"

import { cn } from "@/lib/utils"

function Spinner({ className, ...props }: React.ComponentProps<"svg">) {
  return (
    <Loader2Icon
      role="status"
      aria-label="Loading"
      className={cn("size-4 animate-spin", className)}
      {...props}
    />
  )
}

export { Spinner }

```

---

## `src\components\ui\spotlight-navbar.tsx`

```tsx
"use client";

import React, { useEffect, useRef, useState } from "react";
import { animate } from "framer-motion";
import { cn } from "@/lib/utils";

export interface NavItem {
    label: string;
    href: string;
}

export interface SpotlightNavbarProps {
    items?: NavItem[];
    className?: string;
    onItemClick?: (item: NavItem, index: number) => void;
    defaultActiveIndex?: number;
}

export function SpotlightNavbar({
    items = [
        { label: "Home", href: "#home" },
        { label: "About", href: "#about" },
        { label: "Events", href: "#events" },
        { label: "Sponsors", href: "#sponsors" },
        { label: "Pricing", href: "#pricing" },
    ],
    className,
    onItemClick,
    defaultActiveIndex = 0,
}: SpotlightNavbarProps) {
    const navRef = useRef<HTMLDivElement>(null);
    const [activeIndex, setActiveIndex] = useState(defaultActiveIndex);
    const [hoverX, setHoverX] = useState<number | null>(null);
    const [isDark, setIsDark] = useState(false);

    // Refs for the "light" positions so we can animate them imperatively
    const spotlightX = useRef(0);
    const ambienceX = useRef(0);

    useEffect(() => {
        const checkTheme = () => {
            setIsDark(document.documentElement.classList.contains('dark'));
        };
        checkTheme();
        const observer = new MutationObserver(checkTheme);
        observer.observe(document.documentElement, { attributes: true, attributeFilter: ['class'] });
        return () => observer.disconnect();
    }, []);

    useEffect(() => {
        if (!navRef.current) return;
        const nav = navRef.current;

        const handleMouseMove = (e: MouseEvent) => {
            const rect = nav.getBoundingClientRect();
            const x = e.clientX - rect.left;
            setHoverX(x);
            // Direct update for immediate feedback (no spring for the mouse itself, feels snappier)
            spotlightX.current = x;
            nav.style.setProperty("--spotlight-x", `${x}px`);
        };

        const handleMouseLeave = () => {
            setHoverX(null);
            // When mouse leaves, spring the spotlight back to the active item
            const activeItem = nav.querySelector(`[data-index="${activeIndex}"]`);
            if (activeItem) {
                const navRect = nav.getBoundingClientRect();
                const itemRect = activeItem.getBoundingClientRect();
                const targetX = itemRect.left - navRect.left + itemRect.width / 2;

                animate(spotlightX.current, targetX, {
                    type: "spring",
                    stiffness: 200,
                    damping: 20,
                    onUpdate: (v) => {
                        spotlightX.current = v;
                        nav.style.setProperty("--spotlight-x", `${v}px`);
                    }
                });
            }
        };

        nav.addEventListener("mousemove", handleMouseMove);
        nav.addEventListener("mouseleave", handleMouseLeave);

        return () => {
            nav.removeEventListener("mousemove", handleMouseMove);
            nav.removeEventListener("mouseleave", handleMouseLeave);
        };
    }, [activeIndex]);

    // Handle the "Ambience" (Active Item) Movement
    useEffect(() => {
        if (!navRef.current) return;
        const nav = navRef.current;
        const activeItem = nav.querySelector(`[data-index="${activeIndex}"]`);

        if (activeItem) {
            const navRect = nav.getBoundingClientRect();
            const itemRect = activeItem.getBoundingClientRect();
            const targetX = itemRect.left - navRect.left + itemRect.width / 2;

            animate(ambienceX.current, targetX, {
                type: "spring",
                stiffness: 200,
                damping: 20,
                onUpdate: (v) => {
                    ambienceX.current = v;
                    nav.style.setProperty("--ambience-x", `${v}px`);
                },
            });
        }
    }, [activeIndex]);

    const handleItemClick = (item: NavItem, index: number) => {
        setActiveIndex(index);
        onItemClick?.(item, index);
    };

    return (
        <div className={cn("relative flex justify-center pt-10", className)}>
            <nav
                ref={navRef}
                className={cn(
                    "spotlight-nav spotlight-nav-bg glass-border spotlight-nav-shadow",
                    "relative h-11 rounded-full transition-all duration-300 overflow-hidden"
                )}
            >
                {/* Content */}
                <ul className="relative flex items-center h-full px-2 gap-0 z-[10]">
                    {items.map((item, idx) => (
                        <li key={idx} className="relative h-full flex items-center justify-center">
                            <a
                                href={item.href}
                                data-index={idx}
                                onClick={(e) => {
                                    e.preventDefault();
                                    handleItemClick(item, idx);
                                }}
                                className={cn(
                                    "px-4 py-2 text-sm font-medium transition-colors duration-200 rounded-full",
                                    "focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-neutral-400 dark:focus-visible:ring-white/30",
                                    // Active vs Inactive Text
                                    activeIndex === idx
                                        ? "text-black dark:text-white"
                                        : "text-neutral-500 dark:text-neutral-400 hover:text-black dark:hover:text-white"
                                )}
                            >
                                {item.label}
                            </a>
                        </li>
                    ))}
                </ul>

                {/* LIGHTING LAYERS 
           We use CSS variables --spotlight-x and --ambience-x updated by JS
        */}

                {/* 1. The Moving Spotlight (Follows Mouse) */}
                <div
                    className="pointer-events-none absolute bottom-0 left-0 w-full h-full z-[1] opacity-0 transition-opacity duration-300"
                    style={{
                        opacity: hoverX !== null ? 1 : 0,
                        background: `
              radial-gradient(
                120px circle at var(--spotlight-x) 100%, 
                var(--spotlight-color, rgba(0,0,0,0.1)) 0%, 
                transparent 50%
              )
            `
                    }}
                />

                {/* 2. The Active State Ambience (Stays on Active) */}
                <div
                    className="pointer-events-none absolute bottom-0 left-0 w-full h-[2px] z-[2]"
                    style={{
                        background: `
                  radial-gradient(
                    60px circle at var(--ambience-x) 0%, 
                    var(--ambience-color, rgba(0,0,0,1)) 0%, 
                    transparent 100%
                  )
                `
                    }}
                />

            </nav>

            {/* STYLE BLOCK for Dynamic Colors 
        This allows us to switch the gradient colors cleanly using Tailwind classes 
        without messy inline conditionals.
      */}
            <style jsx>{`
        nav {
          /* Light Mode Colors: Dark Gray/Black lights */
          --spotlight-color: rgba(0,0,0,0.08);
          --ambience-color: rgba(0,0,0,0.8);
        }
        :global(.dark) nav {
          /* Dark Mode Colors: White lights */
          --spotlight-color: rgba(255,255,255,0.15);
          --ambience-color: rgba(255,255,255,1);
        }
      `}</style>
        </div>
    );
}

```

---

## `src\components\ui\stacked-logos.tsx`

```tsx
"use client";

import * as React from "react";
import { cn } from "@/lib/utils";

/* =============================================================================
   StackedLogos Component
   
   Multiple logo sets that animate in/out while stacked on top of each other.
   Uses CSS grid with separator lines for Vercel-style grid pattern.
   Both gradient AND border glow follow mouse position.
============================================================================= */

export interface StackedLogosProps {
  /** Array of logo groups - each group is an array of React nodes */
  logoGroups: React.ReactNode[][];
  /** Animation duration in seconds. Default: 30 */
  duration?: number;
  /** Stagger factor for animation timing between groups. Default: 0 */
  stagger?: number;
  /** Width of each logo container. Default: "200px" */
  logoWidth?: string;
  /** Additional CSS classes */
  className?: string;
}

/**
 * StackedLogos Component
 *
 * Displays multiple logo groups that animate in/out while stacked.
 * Great for showcasing many partners/clients in a compact space.
 */
export const StackedLogos = ({
  logoGroups,
  duration = 30,
  stagger = 0,
  logoWidth = "200px",
  className,
}: StackedLogosProps) => {
  const itemCount = logoGroups[0]?.length || 0;
  const columns = logoGroups.length;
  const containerRef = React.useRef<HTMLDivElement>(null);
  const gridRef = React.useRef<HTMLDivElement>(null);

  // Track mouse position for glow effect
  const handleMouseMove = React.useCallback(
    (e: React.MouseEvent<HTMLDivElement>) => {
      if (!containerRef.current || !gridRef.current) return;

      const rect = gridRef.current.getBoundingClientRect();
      const x = e.clientX - rect.left;
      const y = e.clientY - rect.top;

      containerRef.current.style.setProperty("--mouse-x", `${x}px`);
      containerRef.current.style.setProperty("--mouse-y", `${y}px`);
    },
    [],
  );

  // Calculate border positions for the mask
  const borderWidth = parseInt(logoWidth) || 200;
  const cellHeight = 128; // py-16 = 64px * 2

  return (
    <div
      ref={containerRef}
      className={cn("stacked-logos relative w-auto", className)}
      style={
        {
          "--duration": duration,
          "--items": itemCount,
          "--lists": columns,
          "--stagger": stagger,
          "--logo-width": logoWidth,
          "--cell-height": `${cellHeight}px`,
        } as React.CSSProperties
      }
      onMouseMove={handleMouseMove}
    >
      {/* Grid Container */}
      <div
        ref={gridRef}
        className="grid relative mx-auto w-fit"
        style={{
          gridTemplateColumns: `repeat(${columns}, ${logoWidth})`,
        }}
      >
        {/* Mouse-following glow overlay for background */}
        <div
          className="stacked-logos__glow pointer-events-none absolute inset-0 opacity-0 transition-opacity duration-300 z-10"
          style={{
            background:
              "radial-gradient(500px circle at var(--mouse-x, 0) var(--mouse-y, 0), rgba(251,191,36,0.1), transparent 70%)",
          }}
        />

        {/* Mouse-following glow for borders - single overlay with gradient */}
        <div
          className="stacked-logos__border-glow pointer-events-none absolute inset-0 opacity-0 transition-opacity duration-300 z-20"
          style={{
            background:
              "radial-gradient(600px circle at var(--mouse-x, 0) var(--mouse-y, 0), rgba(251,191,36,1), transparent 40%)",
            maskImage: `
                            repeating-linear-gradient(to right, transparent, transparent calc(${logoWidth} - 1px), black calc(${logoWidth} - 1px), black ${logoWidth}),
                            linear-gradient(to bottom, black 0, black 1px, transparent 1px, transparent calc(100% - 1px), black calc(100% - 1px), black 100%)
                        `,
            WebkitMaskImage: `
                            repeating-linear-gradient(to right, transparent, transparent calc(${logoWidth} - 1px), black calc(${logoWidth} - 1px), black ${logoWidth}),
                            linear-gradient(to bottom, black 0, black 1px, transparent 1px, transparent calc(100% - 1px), black calc(100% - 1px), black 100%)
                        `,
            maskComposite: "add",
            WebkitMaskComposite: "source-over",
          }}
        />

        {/* Left edge glow */}
        <div
          className="stacked-logos__border-glow pointer-events-none absolute top-0 bottom-0 left-0 w-px opacity-0 transition-opacity duration-300 z-20"
          style={{
            background:
              "radial-gradient(600px circle at var(--mouse-x, 0) var(--mouse-y, 0), rgba(251,191,36,1), transparent 40%)",
          }}
        />

        {/* Logo Groups */}
        {logoGroups.map((logos, groupIndex) => (
          <div
            key={groupIndex}
            className="stacked-logos__cell relative grid"
            style={
              {
                "--index": groupIndex,
                gridTemplate: "1fr / 1fr",
              } as React.CSSProperties
            }
          >
            {/* Base border lines - gray */}
            <div className="absolute top-0 bottom-0 right-0 w-px bg-zinc-200 dark:bg-zinc-800" />
            <div className="absolute left-0 right-0 bottom-0 h-px bg-zinc-200 dark:bg-zinc-800" />
            <div className="absolute left-0 right-0 top-0 h-px bg-zinc-200 dark:bg-zinc-800" />
            {groupIndex === 0 && (
              <div className="absolute top-0 bottom-0 left-0 w-px bg-zinc-200 dark:bg-zinc-800" />
            )}

            {/* Stacked logos */}
            {logos.map((logo, logoIndex) => (
              <div
                key={logoIndex}
                className="stacked-logos__item col-start-1 row-start-1 grid place-items-center py-16 px-8"
                data-logo
                style={{ "--i": logoIndex } as React.CSSProperties}
              >
                <div className="stacked-logos__logo w-full h-8 flex items-center justify-center [&>svg]:h-full [&>svg]:w-auto [&>svg]:fill-zinc-700 dark:[&>svg]:fill-zinc-300 [&>img]:h-full [&>img]:w-auto [&>img]:object-contain [&>img]:grayscale [&>img]:brightness-50 dark:[&>img]:brightness-125">
                  {logo}
                </div>
              </div>
            ))}
          </div>
        ))}
      </div>
    </div>
  );
};

StackedLogos.displayName = "StackedLogos";

export default StackedLogos;

```

---

## `src\components\ui\staggered-grid.tsx`

```tsx
'use client'
import React, { useEffect, useRef, useState, useId } from 'react'
import gsap from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'
import imagesLoaded from 'imagesloaded'
import { cn } from '@/lib/utils'
import { FaGithub, FaSlack, FaTwitter } from 'react-icons/fa'

gsap.registerPlugin(ScrollTrigger)

export interface BentoItem {
    id: number | string
    title: string
    subtitle: string
    description: string
    icon: React.ReactNode
    content?: React.ReactNode
    image?: string
}

export interface StaggeredGridProps {
    images: string[]
    bentoItems: BentoItem[]
    centerText?: string
    credits?: {
        madeBy: { text: string; href: string }
        moreDemos: { text: string; href: string }
    }
    className?: string
    showFooter?: boolean
    scroller?: string | Element | Window | null
}

export function StaggeredGrid({
    images,
    bentoItems,
    centerText = "Halcyon",
    credits = {
        madeBy: { text: "@codrops", href: "https://x.com/codrops" },
        moreDemos: { text: "More demos", href: "https://tympanus.net/codrops/demos" }
    },
    className,
    showFooter = true,
    scroller
}: StaggeredGridProps) {
    const [isLoaded, setIsLoaded] = useState(false)
    const gridFullRef = useRef<HTMLDivElement>(null)
    const textRef = useRef<HTMLDivElement>(null)

    // Bento Grid State
    const [activeBento, setActiveBento] = useState<number>(0);

    const splitText = (text: string) => {
        return text.split('').map((char, i) => (
            <span key={i} className="char inline-block" style={{ willChange: 'transform' }}>{char === ' ' ? '\u00A0' : char}</span>
        ))
    }

    useEffect(() => {
        const handleLoad = () => {
            document.body.classList.remove('loading')
            setIsLoaded(true)
        }

        // Wait for background images to load
        // Note: we target both regular images and bento images if possible
        const imgLoad = imagesLoaded(document.querySelectorAll('.grid__item-img'), { background: true }, handleLoad)

        return () => {
            // Cleanup
        }
    }, [])

    useEffect(() => {
        if (!isLoaded) return

        // Animate Text Element
        if (textRef.current) {
            const chars = textRef.current.querySelectorAll('.char')
            gsap.timeline({
                scrollTrigger: {
                    trigger: textRef.current,
                    scroller: scroller || undefined,
                    start: 'top bottom',
                    end: 'center center-=25%',
                    scrub: 1,
                }
            })
                .from(chars, {
                    ease: 'sine.out',
                    yPercent: 300,
                    autoAlpha: 0,
                    stagger: {
                        each: 0.05,
                        from: 'center'
                    }
                })
        }

        // Animate Full Grid
        if (gridFullRef.current) {
            const gridFullItems = gridFullRef.current.querySelectorAll('.grid__item')
            const numColumns = getComputedStyle(gridFullRef.current).getPropertyValue('grid-template-columns').split(' ').length
            const middleColumnIndex = Math.floor(numColumns / 2)

            const columns: Element[][] = Array.from({ length: numColumns }, () => [])
            gridFullItems.forEach((item: any) => {
                const colAttr = item.getAttribute('data-col');
                // Use data-col if available, fallback to a safe index calculation
                const columnIndex = colAttr !== null ? parseInt(colAttr, 10) : 0;
                if (columns[columnIndex]) {
                    columns[columnIndex].push(item)
                }
            })

            columns.forEach((columnItems, columnIndex) => {
                const delayFactor = Math.abs(columnIndex - middleColumnIndex) * 0.2

                gsap.timeline({
                    scrollTrigger: {
                        trigger: gridFullRef.current,
                        scroller: scroller || undefined,
                        start: 'top bottom',
                        end: 'center center',
                        scrub: 1.5,
                    }
                })
                    .from(columnItems, {
                        yPercent: 450,
                        autoAlpha: 0,
                        delay: delayFactor,
                        ease: 'sine.out',
                    })
                    .from(columnItems.map(item => item.querySelector('.grid__item-img')), {
                        transformOrigin: '50% 0%',
                        ease: 'sine.out',
                    }, 0)
            })

            // Specific animation for Bento Container
            const bentoContainer = gridFullRef.current.querySelector('.bento-container')

            if (bentoContainer) {
                const tl = gsap.timeline({
                    scrollTrigger: {
                        trigger: gridFullRef.current,
                        scroller: scroller || undefined,
                        start: 'top top+=15%',
                        end: 'bottom center',
                        scrub: 1,
                        invalidateOnRefresh: true,
                    }
                })

                // Animate Bento Container to move down and scale
                tl.to(bentoContainer, {
                    y: window.innerHeight * 0.1, // Move down relative to grid
                    scale: 1.5, // Scale up the whole group
                    zIndex: 1000,
                    ease: 'power2.out', // Smooth easing
                    duration: 1,
                    force3D: true // Force hardware acceleration
                }, 0)
            }
        }
    }, [isLoaded])

    // Prepare grid items: fill up to the end of Row 3 (21 slots)
    // This perfectly balances the 3rd row with 2 cards on each side of the bento container.
    const mixedGridItems: (string | 'BENTO_GROUP')[] = Array.from({ length: 21 }, (_, i) => images[i % images.length]);

    // Replace the slot where we want the bento group
    // Position at index 16 = Row 3 (middle row), spanning columns 3-5 (center)
    mixedGridItems[16] = 'BENTO_GROUP';

    return (
        <div
            className={cn("shadow relative overflow-hidden w-full", className)}
            style={{
                '--grid-item-translate': '0px',
            } as React.CSSProperties}
        >
            <section className="grid place-items-center w-full relative mt-[10vh]">
                <div ref={textRef} className="text font-alt uppercase flex content-center text-[clamp(3rem,14vw,10rem)] leading-[0.7] text-neutral-900 dark:text-white">
                    {splitText(centerText)}
                </div>
            </section>

            <section className="grid place-items-center w-full relative">
                <div ref={gridFullRef} className="grid--full relative w-full my-[10vh] h-auto aspect-[1.1] max-w-none p-4 grid gap-4 grid-cols-7 grid-rows-5">
                    <div className="grid-overlay absolute inset-0 z-[15] pointer-events-none opacity-0 bg-white/80 dark:bg-black/80 rounded-lg transition-opacity duration-500" />
                    {mixedGridItems.map((item, i) => {
                        if (item === 'BENTO_GROUP') {
                            // Render the HoverExpand Group using passed bentoItems
                            if (!bentoItems || bentoItems.length === 0) return null;

                            return (
                                <div key="bento-group" data-col={2} className="grid__item bento-container col-span-3 row-span-1 relative z-20 flex items-center justify-center gap-2 h-full w-full will-change-transform">
                                    {bentoItems.map((bentoItem, index) => {
                                        const isActive = activeBento === index;
                                        return (
                                            <div
                                                key={bentoItem.id}
                                                className={cn(
                                                    "relative cursor-pointer overflow-hidden rounded-2xl h-full transition-all duration-700 ease-[cubic-bezier(0.25,1,0.5,1)]",
                                                    isActive
                                                        ? "bg-zinc-900/10 shadow-2xl"
                                                        : "bg-zinc-950"
                                                )}
                                                style={{ width: isActive ? "60%" : "20%" }}
                                                onMouseEnter={() => setActiveBento(index)}
                                                onClick={() => setActiveBento(index)}
                                            >
                                                {/* Border Overlay - Fixes edge artifacts by sitting on top */}
                                                <div className={cn(
                                                    "absolute inset-0 rounded-2xl border z-50 pointer-events-none transition-colors duration-700",
                                                    isActive
                                                        ? "border-zinc-500/50"
                                                        : "border-zinc-800/50 group-hover:border-zinc-700"
                                                )} />

                                                {/* Content Container */}
                                                <div className="relative z-10 w-full h-full flex flex-col p-0">
                                                    {/* Active State Content */}
                                                    <div className={cn(
                                                        "absolute inset-0 flex flex-col transition-all duration-500 ease-in-out",
                                                        isActive ? "opacity-100 translate-y-0" : "opacity-0 translate-y-4 pointer-events-none"
                                                    )}>
                                                        {/* Image - Full Coverage */}
                                                        <div className="absolute inset-0 bg-zinc-900 overflow-hidden z-0 group/img">
                                                            {bentoItem.image && (
                                                                <>
                                                                    <img
                                                                        src={bentoItem.image}
                                                                        alt={bentoItem.title}
                                                                        className="absolute inset-0 w-full h-full object-cover transition-transform duration-700 opacity-90 group-hover/img:opacity-100"
                                                                    />
                                                                    {/* Text Protection Gradient - Shadow peaking from bottom */}
                                                                    <div className="absolute bottom-0 left-0 w-full h-40 bg-gradient-to-t from-black via-black/50 to-transparent pointer-events-none" />
                                                                </>
                                                            )}
                                                        </div>

                                                        {/* Footer Row - Full Width with Shadow */}
                                                        <div className="absolute bottom-0 left-0 w-full h-20 flex items-center justify-between px-5 z-20">


                                                            <div className="flex flex-col relative z-10">
                                                                <h3 className="text-sm font-bold text-white drop-shadow-md leading-none tracking-tight">{bentoItem.title}</h3>
                                                            </div>
                                                            <div className="text-white/90 transition-colors hover:text-white drop-shadow-md relative z-10">
                                                                {bentoItem.icon}
                                                            </div>
                                                        </div>
                                                    </div>
                                                </div>

                                                {/* Inactive State - Icon + Title - Centered */}
                                                <div className={cn(
                                                    "absolute inset-0 flex flex-col items-center justify-center gap-2 transition-all duration-500",
                                                    isActive ? "opacity-0 scale-90 pointer-events-none" : "opacity-100 scale-100"
                                                )}>
                                                    <div className="text-white/50 group-hover:text-white transition-colors">
                                                        {bentoItem.icon}
                                                    </div>
                                                    <span className="text-[10px] font-medium text-zinc-500 group-hover:text-zinc-300 transition-colors uppercase tracking-wider">{bentoItem.title}</span>
                                                </div>
                                            </div>
                                        )
                                    })}
                                </div>
                            )
                        }

                        // Skip rendering for the slots that the group takes up
                        // Group starts at 16, takes 17, 18.
                        if (i === 17 || i === 18) return null;

                        if (typeof item === 'string') {
                            const Icon = i % 3 === 0 ? FaGithub : i % 3 === 1 ? FaSlack : FaTwitter;
                            const label = i % 3 === 0 ? "Github" : i % 3 === 1 ? "Slack" : "Twitter";

                            return (
                                <figure key={`img-${i}`} data-col={i % 7} className="grid__item m-0 relative z-10 [perspective:800px] will-change-[transform,opacity] group cursor-pointer">
                                    <div className="grid__item-img w-full h-full [backface-visibility:hidden] will-change-transform rounded-xl overflow-hidden shadow-sm border border-zinc-200 dark:border-zinc-900 bg-zinc-100 dark:bg-zinc-950 flex items-center justify-center transition-all duration-500 ease-out group-hover:scale-105 group-hover:shadow-xl group-hover:border-transparent">

                                        {/* Gradient Overlay for Hover */}
                                        <div className="absolute inset-0 bg-gradient-to-b from-black/40 via-black/80 to-black backdrop-blur-[2px] opacity-0 group-hover:opacity-100 transition-opacity duration-500 z-0" />

                                        {/* Content Container */}
                                        <div className="relative z-10 flex flex-col items-center justify-center gap-3">
                                            {/* Icon */}
                                            <Icon className="w-8 h-8 text-zinc-400 dark:text-zinc-500 transition-all duration-300 group-hover:text-white group-hover:scale-110" />

                                            {/* Text Reveal */}
                                            <div className="text-center opacity-0 group-hover:opacity-100 transform translate-y-2 group-hover:translate-y-0 transition-all duration-300 delay-75">
                                                <span className="block text-[10px] font-medium text-white/90 uppercase tracking-wider mb-0.5">Build with</span>
                                                <span className="block text-sm font-bold text-white tracking-tight">{label}</span>
                                            </div>
                                        </div>
                                    </div>
                                </figure>
                            )
                        }
                        return null;
                    })}
                </div>
            </section >

            {showFooter && (
                <footer className="frame__footer w-full p-8 flex justify-between items-center relative z-50 text-neutral-900 dark:text-white uppercase font-medium text-xs tracking-wider">
                    <a href={credits.madeBy.href} className="hover:opacity-60 transition-opacity">{credits.madeBy.text}</a>
                    <a href={credits.moreDemos.href} className="hover:opacity-60 transition-opacity">{credits.moreDemos.text}</a>
                </footer>
            )}
        </div >
    )
}

export default StaggeredGrid

```

---

## `src\components\ui\switch.tsx`

```tsx
"use client"

import * as React from "react"
import * as SwitchPrimitive from "@radix-ui/react-switch"

import { cn } from "@/lib/utils"

function Switch({
  className,
  ...props
}: React.ComponentProps<typeof SwitchPrimitive.Root>) {
  return (
    <SwitchPrimitive.Root
      data-slot="switch"
      className={cn(
        "peer data-[state=checked]:bg-primary data-[state=unchecked]:bg-input focus-visible:border-ring focus-visible:ring-ring/50 dark:data-[state=unchecked]:bg-input/80 inline-flex h-[1.15rem] w-8 shrink-0 items-center rounded-full border border-transparent shadow-xs transition-all outline-none focus-visible:ring-[3px] disabled:cursor-not-allowed disabled:opacity-50",
        className
      )}
      {...props}
    >
      <SwitchPrimitive.Thumb
        data-slot="switch-thumb"
        className={cn(
          "bg-background dark:data-[state=unchecked]:bg-foreground dark:data-[state=checked]:bg-primary-foreground pointer-events-none block size-4 rounded-full ring-0 transition-transform data-[state=checked]:translate-x-[calc(100%-2px)] data-[state=unchecked]:translate-x-0"
        )}
      />
    </SwitchPrimitive.Root>
  )
}

export { Switch }

```

---

## `src\components\ui\table.tsx`

```tsx
"use client"

import * as React from "react"

import { cn } from "@/lib/utils"

function Table({ className, ...props }: React.ComponentProps<"table">) {
  return (
    <div
      data-slot="table-container"
      className="relative w-full overflow-x-auto"
    >
      <table
        data-slot="table"
        className={cn("w-full caption-bottom text-sm", className)}
        {...props}
      />
    </div>
  )
}

function TableHeader({ className, ...props }: React.ComponentProps<"thead">) {
  return (
    <thead
      data-slot="table-header"
      className={cn("[&_tr]:border-b", className)}
      {...props}
    />
  )
}

function TableBody({ className, ...props }: React.ComponentProps<"tbody">) {
  return (
    <tbody
      data-slot="table-body"
      className={cn("[&_tr:last-child]:border-0", className)}
      {...props}
    />
  )
}

function TableFooter({ className, ...props }: React.ComponentProps<"tfoot">) {
  return (
    <tfoot
      data-slot="table-footer"
      className={cn(
        "bg-muted/50 border-t font-medium [&>tr]:last:border-b-0",
        className
      )}
      {...props}
    />
  )
}

function TableRow({ className, ...props }: React.ComponentProps<"tr">) {
  return (
    <tr
      data-slot="table-row"
      className={cn(
        "hover:bg-muted/50 data-[state=selected]:bg-muted border-b transition-colors",
        className
      )}
      {...props}
    />
  )
}

function TableHead({ className, ...props }: React.ComponentProps<"th">) {
  return (
    <th
      data-slot="table-head"
      className={cn(
        "text-foreground h-10 px-2 text-left align-middle font-medium whitespace-nowrap [&:has([role=checkbox])]:pr-0 [&>[role=checkbox]]:translate-y-[2px]",
        className
      )}
      {...props}
    />
  )
}

function TableCell({ className, ...props }: React.ComponentProps<"td">) {
  return (
    <td
      data-slot="table-cell"
      className={cn(
        "p-2 align-middle whitespace-nowrap [&:has([role=checkbox])]:pr-0 [&>[role=checkbox]]:translate-y-[2px]",
        className
      )}
      {...props}
    />
  )
}

function TableCaption({
  className,
  ...props
}: React.ComponentProps<"caption">) {
  return (
    <caption
      data-slot="table-caption"
      className={cn("text-muted-foreground mt-4 text-sm", className)}
      {...props}
    />
  )
}

export {
  Table,
  TableHeader,
  TableBody,
  TableFooter,
  TableHead,
  TableRow,
  TableCell,
  TableCaption,
}

```

---

## `src\components\ui\tabs.tsx`

```tsx
"use client";

import * as React from "react";
import { cn } from "@/lib/utils";

export const TabsContext = React.createContext<{
  activeTab: string;
  setActiveTab: (v: string) => void;
}>({ activeTab: "", setActiveTab: () => {} });

interface TabsProps {
  children: React.ReactNode;
  defaultValue?: string;
  value?: string;
  onValueChange?: (value: string) => void;
  className?: string;
}

export function Tabs({ children, defaultValue = "", value, onValueChange, className }: TabsProps) {
  const [internalActiveTab, setInternalActiveTab] = React.useState(defaultValue);
  const activeTab = value ?? internalActiveTab;

  const setActiveTab = React.useCallback(
    (nextValue: string) => {
      if (value === undefined) {
        setInternalActiveTab(nextValue);
      }
      onValueChange?.(nextValue);
    },
    [onValueChange, value]
  );

  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      <div className={cn("w-full", className)}>{children}</div>
    </TabsContext.Provider>
  );
}

export function TabsList({ children, className }: { children: React.ReactNode; className?: string }) {
  return (
    <div className={cn("inline-flex h-10 items-center justify-center rounded-lg bg-neutral-100 dark:bg-zinc-800/80 p-1 text-neutral-500 dark:text-zinc-400 mb-4", className)}>
      {children}
    </div>
  );
}

export function TabsTrigger({ value, children, className }: { value: string; children: React.ReactNode; className?: string }) {
  const { activeTab, setActiveTab } = React.useContext(TabsContext);
  const isActive = activeTab === value;
  
  return (
    <button
      onClick={() => setActiveTab(value)}
      className={cn(
        "inline-flex items-center justify-center whitespace-nowrap rounded-md px-3 py-1.5 text-sm font-medium transition-all focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-zinc-400 disabled:pointer-events-none disabled:opacity-50",
        isActive 
          ? "bg-white dark:bg-zinc-700/80 text-neutral-900 dark:text-white shadow-sm" 
          : "text-neutral-500 dark:text-zinc-400 hover:text-neutral-900 dark:hover:text-zinc-200",
        className
      )}
    >
      {children}
    </button>
  );
}

export function TabsContent({ value, children, className }: { value: string; children: React.ReactNode; className?: string }) {
  const { activeTab } = React.useContext(TabsContext);
  const isActive = activeTab === value;
  
  return (
    <div 
      className={cn(
        "mt-4 outline-none",
        isActive ? "block" : "hidden",
        className
      )}
    >
      {children}
    </div>
  );
}

```

---

## `src\components\ui\testimonials-card.tsx`

```tsx
"use client";

import React, { useState, useMemo } from "react";
import { motion, AnimatePresence } from "framer-motion";
import { cn } from "@/lib/utils";
import { ArrowLeft, ArrowRight } from "lucide-react";

interface TestimonialItem {
    /** Unique identifier for the card */
    id: string | number;
    /** Title displayed for the card */
    title: string;
    /** Description text for the card */
    description: string;
    /** Image URL/path for the card */
    image: string;
}

interface TestimonialsCardProps {
    /** Array of testimonial items to display */
    items: TestimonialItem[];
    /** Additional CSS classes for the container */
    className?: string;
    /** Width of the card stack (default: 400) */
    width?: number;
    /** Whether to show navigation arrows (default: true) */
    showNavigation?: boolean;
    /** Whether to show the counter (default: true) */
    showCounter?: boolean;
    /** Whether to enable auto-play (default: false) */
    autoPlay?: boolean;
    /** Auto-play interval in ms (default: 3000) */
    autoPlayInterval?: number;
}

export function TestimonialsCard({
    items,
    className,
    width = 400,
    showNavigation = true,
    showCounter = true,
    autoPlay = false,
    autoPlayInterval = 3000,
}: TestimonialsCardProps) {
    const [activeIndex, setActiveIndex] = useState(0);
    const [direction, setDirection] = useState(1);

    const activeItem = items[activeIndex];

    // Auto-play effect
    React.useEffect(() => {
        if (!autoPlay || items.length <= 1) return;

        const interval = setInterval(() => {
            setDirection(1);
            setActiveIndex((prev) => (prev + 1) % items.length);
        }, autoPlayInterval);

        return () => clearInterval(interval);
    }, [autoPlay, autoPlayInterval, items.length]);

    const handleNext = () => {
        if (activeIndex < items.length - 1) {
            setDirection(1);
            setActiveIndex(activeIndex + 1);
        }
    };

    const handlePrev = () => {
        if (activeIndex > 0) {
            setDirection(-1);
            setActiveIndex(activeIndex - 1);
        }
    };

    // Pre-calculate rotations for visual variety
    const rotations = useMemo(() => [4, -2, -9, 7], []);

    if (!items || items.length === 0) {
        return null;
    }

    return (
        <div className={cn("flex items-center justify-center p-8", className)}>
            <div
                className="relative grid  grid-cols-[1fr]  md:grid-cols-[1fr_1fr] md:grid-rows-[auto_auto_auto] gap-x-8 gap-y-2 w-full "
                style={{ perspective: "1400px", maxWidth: `${width}px` }}
            >
                {/* Counter */}
                {showCounter && (
                    <div className="row-start-1 md:col-start-2 md:row-start-1 text-right font-mono text-sm text-neutral-500 dark:text-neutral-400">
                        {activeIndex + 1} / {items.length}
                    </div>
                )}

                {/* Image Card Stack */}
                <div className="row-start-2  col-start-1 md:row-start-1 row-span-3 relative w-full aspect-square">
                    <AnimatePresence custom={direction}>
                        {items.map((item, index) => {
                            const isActive = index === activeIndex;
                            const offset = index - activeIndex;

                            return (
                                <motion.div
                                    key={item.id}
                                    className="absolute inset-0 w-full h-full overflow-hidden border-[6px] bg-neutral-200 dark:bg-neutral-800 border-white dark:border-neutral-700 shadow-2xl rounded-lg"
                                    initial={{
                                        x: offset * 15,
                                        y: Math.abs(offset) * 6,
                                        z: -150 * Math.abs(offset),
                                        scale: 0.85 - Math.abs(offset) * 0.04,
                                        rotateZ: rotations[index % 4],
                                        opacity: isActive ? 1 : 0.5,
                                        zIndex: 10 - Math.abs(offset),
                                    }}
                                    animate={
                                        isActive
                                            ? {
                                                x: [offset * 15, direction === 1 ? -200 : 200, 0],
                                                y: [Math.abs(offset) * 6, 0, 0],
                                                z: [-200, 150, 250],
                                                scale: [0.85, 1.05, 1],
                                                rotateZ: [rotations[index % 4], -5, 0],
                                                opacity: 1,
                                                zIndex: 100,
                                            }
                                            : {
                                                x: offset * 15,
                                                y: Math.abs(offset) * 6,
                                                z: -150 * Math.abs(offset),
                                                rotateZ: rotations[index % 4],
                                                scale: 0.85 - Math.abs(offset) * 0.04,
                                                opacity: 0.55,
                                                zIndex: 10 - Math.abs(offset),
                                            }
                                    }
                                    exit={{
                                        x: direction === 1 ? -250 : 250,
                                        z: -260,
                                        scale: 0.75,
                                        rotateZ: direction === 1 ? -10 : 10,
                                        opacity: 0,
                                    }}
                                    transition={{
                                        duration: 0.75,
                                        ease: [0.22, 1, 0.36, 1],
                                    }}
                                >
                                    <img
                                        src={item.image}
                                        alt={item.title}
                                        className="w-full h-full object-cover"
                                        draggable={false}
                                    />
                                </motion.div>
                            );
                        })}
                    </AnimatePresence>
                </div>

                {/* Text Area */}
                <div className="col-start-1 md:col-start-2   md:row-start-1 flex flex-col justify-center min-h-[120px]">
                    <AnimatePresence mode="wait">
                        <motion.div
                            key={activeItem.id}
                            initial={{ opacity: 0, y: 25 }}
                            animate={{ opacity: 1, y: 0 }}
                            exit={{ opacity: 0, y: -25 }}
                            transition={{ duration: 0.35 }}
                        >
                            <h3 className="text-xl font-bold text-neutral-900 dark:text-white">
                                {activeItem.title}
                            </h3>
                            <p className="text-sm text-neutral-600 dark:text-neutral-400 mt-2">
                                {activeItem.description}
                            </p>
                        </motion.div>
                    </AnimatePresence>
                </div>

                {/* Navigation Controls */}
                {showNavigation && items.length > 1 && (
                    <div className="col-start-1 md:col-start-2 md:row-start-3 flex gap-2  m-auto -mt-2 md:mt-4  md:m-0">
                        <button
                            disabled={activeIndex === 0}
                            onClick={handlePrev}
                            className={cn(
                                "flex items-center justify-center w-10 h-10 rounded-full border border-neutral-200 dark:border-neutral-700 bg-white dark:bg-neutral-800 transition-all",
                                activeIndex === 0
                                    ? "opacity-50 cursor-not-allowed"
                                    : "hover:bg-neutral-100 dark:hover:bg-neutral-700 hover:scale-105"
                            )}
                            aria-label="Previous card"
                        >
                            <ArrowLeft className="w-4 h-4 text-neutral-700 dark:text-neutral-300" />
                        </button>
                        <button
                            disabled={activeIndex === items.length - 1}
                            onClick={handleNext}
                            className={cn(
                                "flex items-center justify-center w-10 h-10 rounded-full border border-neutral-200 dark:border-neutral-700 bg-white dark:bg-neutral-800 transition-all",
                                activeIndex === items.length - 1
                                    ? "opacity-50 cursor-not-allowed"
                                    : "hover:bg-neutral-100 dark:hover:bg-neutral-700 hover:scale-105"
                            )}
                            aria-label="Next card"
                        >
                            <ArrowRight className="w-4 h-4 text-neutral-700 dark:text-neutral-300" />
                        </button>
                    </div>
                )}
            </div>
        </div>
    );
}

export default TestimonialsCard;

```

---

## `src\components\ui\testinomial-card2.tsx`

```tsx

import { cn } from "@/lib/utils";
import { FaXTwitter } from "react-icons/fa6";


interface TestimonialItem {
    title: string;
    company?: string;
    description: string;
    image: string;
    href?: string;
    big?: boolean;
}

interface TestimonialsCardProps {
    items: TestimonialItem[];
    className?: string;
}

export function TestimonialsCard2({ items, className }: TestimonialsCardProps) {
    return (
        <div className={cn("flex flex-row gap-4", className)}>
            {items?.length > 0 && items.map((el, idx) => (
                <div
                    key={idx}
                    className="flex flex-col justify-between flex-shrink-0 w-72 bg-foreground text-background rounded-xl px-5 py-4 transition-all duration-300 hover:scale-[1.02] hover:shadow-lg"
                >
                    <div>
                        <p className="text-sm leading-relaxed opacity-90">{el.description}</p>
                    </div>
                    <div className="flex items-center gap-3 mt-4 pt-3 border-t border-background/10">
                        <div className="rounded-full overflow-hidden size-9 flex-shrink-0">
                            <img
                                src={el.image}
                                alt={el.title}
                                className="w-full h-full object-cover"
                                draggable={false}
                            />
                        </div>
                        <div className="flex-1 min-w-0">
                            <h1 className="text-sm font-semibold truncate">{el.title}</h1>
                            <p className="text-xs opacity-60 truncate">{el.company}</p>
                        </div>
                        {el.href && (
                            <a
                                href={el.href}
                                target="_blank"
                                rel="noopener noreferrer"
                                className="opacity-50 hover:opacity-100 transition-opacity flex-shrink-0"
                                onClick={e => e.stopPropagation()}
                            >
                                <FaXTwitter className="size-3.5" />
                            </a>
                        )}
                    </div>
                </div>
            ))}
        </div>
    );
}

export default TestimonialsCard2;

```

---

## `src\components\ui\textarea.tsx`

```tsx
import * as React from 'react'

import { cn } from '@/lib/utils'

function Textarea({ className, ...props }: React.ComponentProps<'textarea'>) {
    return (
        <textarea
            data-slot="textarea"
            className={cn('border-input placeholder:text-muted-foreground focus-visible:border-ring focus-visible:ring-ring/50 aria-invalid:ring-destructive/20 dark:aria-invalid:ring-destructive/40 aria-invalid:border-destructive field-sizing-content shadow-xs flex min-h-16 w-full rounded-md border bg-transparent px-3 py-2 text-base outline-none transition-[color,box-shadow] focus-visible:ring-[3px] disabled:cursor-not-allowed disabled:opacity-50 md:text-sm', className)}
            {...props}
        />
    )
}

export { Textarea }

```

---

## `src\components\ui\toggle-group.tsx`

```tsx
'use client'

import * as React from 'react'
import * as ToggleGroupPrimitive from '@radix-ui/react-toggle-group'
import { type VariantProps } from 'class-variance-authority'

import { cn } from '@/lib/utils'
import { toggleVariants } from './toggle'

const ToggleGroupContext = React.createContext<VariantProps<typeof toggleVariants>>({
    size: 'default',
    variant: 'default',
})

function ToggleGroup({ className, variant, size, children, ...props }: React.ComponentProps<typeof ToggleGroupPrimitive.Root> & VariantProps<typeof toggleVariants>) {
    return (
        <ToggleGroupPrimitive.Root
            data-slot="toggle-group"
            data-variant={variant}
            data-size={size}
            className={cn('group/toggle-group data-[variant=outline]:shadow-xs flex w-fit items-center rounded-md', className)}
            {...props}>
            <ToggleGroupContext.Provider value={{ variant, size }}>{children}</ToggleGroupContext.Provider>
        </ToggleGroupPrimitive.Root>
    )
}

function ToggleGroupItem({ className, children, variant, size, ...props }: React.ComponentProps<typeof ToggleGroupPrimitive.Item> & VariantProps<typeof toggleVariants>) {
    const context = React.useContext(ToggleGroupContext)

    return (
        <ToggleGroupPrimitive.Item
            data-slot="toggle-group-item"
            data-variant={context.variant || variant}
            data-size={context.size || size}
            className={cn(
                toggleVariants({
                    variant: context.variant || variant,
                    size: context.size || size,
                }),
                'min-w-0 flex-1 shrink-0 rounded-none shadow-none first:rounded-l-md last:rounded-r-md focus:z-10 focus-visible:z-10 data-[variant=outline]:border-l-0 data-[variant=outline]:first:border-l',
                className
            )}
            {...props}>
            {children}
        </ToggleGroupPrimitive.Item>
    )
}

export { ToggleGroup, ToggleGroupItem }

```

---

## `src\components\ui\toggle.tsx`

```tsx
'use client'

import * as React from 'react'
import * as TogglePrimitive from '@radix-ui/react-toggle'
import { cva, type VariantProps } from 'class-variance-authority'

import { cn } from '@/lib/utils'

const toggleVariants = cva(
    "inline-flex items-center justify-center gap-2 rounded-md text-sm font-medium hover:bg-muted hover:text-muted-foreground disabled:pointer-events-none disabled:opacity-50 data-[state=on]:bg-accent data-[state=on]:text-accent-foreground [&_svg]:pointer-events-none [&_svg:not([class*='size-'])]:size-4 [&_svg]:shrink-0 focus-visible:border-ring focus-visible:ring-ring/50 focus-visible:ring-[3px] outline-none transition-[color,box-shadow] aria-invalid:ring-destructive/20 dark:aria-invalid:ring-destructive/40 aria-invalid:border-destructive whitespace-nowrap",
    {
        variants: {
            variant: {
                default: 'bg-transparent',
                outline: 'border border-input bg-transparent shadow-xs hover:bg-accent hover:text-accent-foreground',
            },
            size: {
                default: 'h-9 px-2 min-w-9',
                sm: 'h-8 px-1.5 min-w-8',
                lg: 'h-10 px-2.5 min-w-10',
            },
        },
        defaultVariants: {
            variant: 'default',
            size: 'default',
        },
    }
)

function Toggle({ className, variant, size, ...props }: React.ComponentProps<typeof TogglePrimitive.Root> & VariantProps<typeof toggleVariants>) {
    return (
        <TogglePrimitive.Root
            data-slot="toggle"
            className={cn(toggleVariants({ variant, size, className }))}
            {...props}
        />
    )
}

export { Toggle, toggleVariants }

```

---

## `src\components\ui\tooltip.tsx`

```tsx
'use client'

import * as React from 'react'
import * as TooltipPrimitive from '@radix-ui/react-tooltip'

import { cn } from '@/lib/utils'

function TooltipProvider({ delayDuration = 0, ...props }: React.ComponentProps<typeof TooltipPrimitive.Provider>) {
    return (
        <TooltipPrimitive.Provider
            data-slot="tooltip-provider"
            delayDuration={delayDuration}
            {...props}
        />
    )
}

function Tooltip({ ...props }: React.ComponentProps<typeof TooltipPrimitive.Root>) {
    return (
        <TooltipProvider>
            <TooltipPrimitive.Root
                data-slot="tooltip"
                {...props}
            />
        </TooltipProvider>
    )
}

function TooltipTrigger({ ...props }: React.ComponentProps<typeof TooltipPrimitive.Trigger>) {
    return (
        <TooltipPrimitive.Trigger
            data-slot="tooltip-trigger"
            {...props}
        />
    )
}

function TooltipContent({ className, sideOffset = 4, children, ...props }: React.ComponentProps<typeof TooltipPrimitive.Content>) {
    return (
        <TooltipPrimitive.Portal>
            <TooltipPrimitive.Content
                data-slot="tooltip-content"
                sideOffset={sideOffset}
                className={cn(
                    'bg-card text-foreground animate-in fade-in-0 zoom-in-95 data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=closed]:zoom-out-95 data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-from-right-2 data-[side=right]:slide-in-from-left-2 data-[side=top]:slide-in-from-bottom-2 rounded-(--radius) ring-foreground/10 z-50 max-w-sm border border-transparent px-3 py-2 text-xs shadow-md shadow-black/10 ring-1 backdrop-blur dark:border-white/5 dark:bg-zinc-900/75 dark:shadow-lg dark:shadow-black/20 dark:ring-black/75',
                    className
                )}
                {...props}>
                {children}
            </TooltipPrimitive.Content>
        </TooltipPrimitive.Portal>
    )
}

export { Tooltip, TooltipTrigger, TooltipContent, TooltipProvider }

```

---

## `src\components\ui\twisting-ribbon.tsx`

```tsx
"use client";

import React, { useEffect, useRef } from "react";
import { cn } from "@/lib/utils";

export interface RibbonColors {
  face?: string;
  foldA?: string;
  foldB?: string;
  foldC?: string;
}

export interface TwistingRibbonProps extends React.HTMLAttributes<HTMLDivElement> {
  /** Number of segments along the ribbon. Higher = smoother but more expensive (default 400). */
  segments?: number;
  /** Speed of the wave motion (default 0.018) */
  waveSpeed?: number;
  /** Scale factor for the wave amplitude (default 1) */
  waveAmplitude?: number;
  /** Number of full twists along the ribbon length (default 6) */
  twistCycles?: number;
  /** Custom colors for light mode (accepts hex strings like "#ff3c0a") */
  lightColors?: RibbonColors;
  /** Custom colors for dark mode (accepts hex strings like "#1e2024") */
  darkColors?: RibbonColors;
}

// Helper to convert hex to RGB array
function hexToRgb(hex: string): [number, number, number] {
  hex = hex.replace(/^#/, "");
  if (hex.length === 3) hex = hex.split("").map((c) => c + c).join("");
  const num = parseInt(hex, 16);
  return [num >> 16, (num >> 8) & 255, num & 255];
}

export function TwistingRibbon({
  className,
  segments = 400,
  waveSpeed = 0.018,
  waveAmplitude = 1,
  twistCycles = 6,
  lightColors,
  darkColors,
  ...props
}: TwistingRibbonProps) {
  const canvasRef = useRef<HTMLCanvasElement>(null);
  const containerRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    const canvas = canvasRef.current;
    const container = containerRef.current;
    if (!canvas || !container) return;

    const ctx = canvas.getContext("2d");
    if (!ctx) return;

    let animationFrameId: number;
    let width = container.clientWidth;
    let height = container.clientHeight;

    // ── Configuration ───────────────────────────────────────────────────
    const RIBBON_HALF_W = 14; 
    const RIBBON_X_SCALE = 1.4; 
    const RIBBON_X_OFFSET = 0.2; 

    // ── Wave / motion ─────────────────────────────────────────────────────
    const WAVE1_FREQ = 3.5;
    const WAVE1_TIME_SPEED = 0.7;
    const WAVE1_AMP = 110 * waveAmplitude;
    const WAVE2_FREQ = 7.0;
    const WAVE2_TIME_SPEED = 1.1;
    const WAVE2_AMP = 30 * waveAmplitude;

    const TWIST_TIME_SPEED = 0.5;

    // ── Color palette ─────────────────────────────────────────────────────
    // Light Mode Colors (defaults)
    const L_COLOR_FACE = lightColors?.face ? hexToRgb(lightColors.face) : [255, 60, 10];
    const L_COLOR_FOLD_A = lightColors?.foldA ? hexToRgb(lightColors.foldA) : [180, 255, 0];
    const L_COLOR_FOLD_B = lightColors?.foldB ? hexToRgb(lightColors.foldB) : [60, 80, 255];
    const L_COLOR_FOLD_C = lightColors?.foldC ? hexToRgb(lightColors.foldC) : [0, 220, 255];
    const L_SHADOW_COLOR = [80, 60, 40];
    const L_SHADOW_ALPHA = 14 / 255;
    const L_EDGE_COLOR = [0, 0, 0];
    const L_EDGE_ALPHA = 22 / 255;

    // Dark Mode Colors (Restore original vibrant colors for dark mode)
    const D_COLOR_FACE = darkColors?.face ? hexToRgb(darkColors.face) : [255, 60, 10];
    const D_COLOR_FOLD_A = darkColors?.foldA ? hexToRgb(darkColors.foldA) : [180, 255, 0];
    const D_COLOR_FOLD_B = darkColors?.foldB ? hexToRgb(darkColors.foldB) : [60, 80, 255];
    const D_COLOR_FOLD_C = darkColors?.foldC ? hexToRgb(darkColors.foldC) : [0, 220, 255];
    const D_SHADOW_COLOR = [0, 0, 0];
    const D_SHADOW_ALPHA = 120 / 255;
    const D_EDGE_COLOR = [255, 255, 255];
    const D_EDGE_ALPHA = 30 / 255;

    const COLOR_CYCLE_FREQ = 2.0;
    const COLOR_CYCLE_SPEED = 0.3;
    const FACE_BLEND_GAMMA = 1.2;

    const SHADOW_OFFSET_X = 4;
    const SHADOW_OFFSET_Y = 7;
    const EDGE_MIN_TWIST = 0.08;
    const EDGE_WEIGHT = 0.5;

    let t = 0;

    function resize() {
      width = container!.clientWidth;
      height = container!.clientHeight;
      canvas!.width = width * window.devicePixelRatio;
      canvas!.height = height * window.devicePixelRatio;
      ctx!.scale(window.devicePixelRatio, window.devicePixelRatio);
    }

    window.addEventListener("resize", resize);
    resize();

    // ── Helper functions ────────────────────────────────────────────────
    function lerpColor(a: number[], b: number[], f: number) {
      return [
        Math.round(a[0] + (b[0] - a[0]) * f),
        Math.round(a[1] + (b[1] - a[1]) * f),
        Math.round(a[2] + (b[2] - a[2]) * f),
      ];
    }

    function buildSpine(time: number) {
      const pts = [];
      for (let i = 0; i <= segments; i++) {
        const progress = i / segments;
        pts.push({
          x: progress * width * RIBBON_X_SCALE - width * RIBBON_X_OFFSET,
          y:
            height / 2 +
            Math.sin(progress * Math.PI * WAVE1_FREQ + time * WAVE1_TIME_SPEED) * WAVE1_AMP +
            Math.sin(progress * Math.PI * WAVE2_FREQ + time * WAVE2_TIME_SPEED) * WAVE2_AMP,
        });
      }
      return pts;
    }

    function buildNormals(pts: { x: number; y: number }[]) {
      const last = pts.length - 1;
      return pts.map((_, i) => {
        const dx =
          i === 0
            ? pts[1].x - pts[0].x
            : i === last
            ? pts[last].x - pts[last - 1].x
            : pts[i + 1].x - pts[i - 1].x;
        const dy =
          i === 0
            ? pts[1].y - pts[0].y
            : i === last
            ? pts[last].y - pts[last - 1].y
            : pts[i + 1].y - pts[i - 1].y;
        const len = Math.sqrt(dx * dx + dy * dy) || 1;
        return { nx: -dy / len, ny: dx / len };
      });
    }

    function buildEdges(
      pts: { x: number; y: number }[],
      normals: { nx: number; ny: number }[],
      time: number
    ) {
      const tops = [];
      const bots = [];
      const twists = [];
      for (let i = 0; i <= segments; i++) {
        const twist = Math.cos(
          (i / segments) * Math.PI * twistCycles + time * TWIST_TIME_SPEED
        );
        const w = RIBBON_HALF_W * Math.abs(twist);
        const sign = twist >= 0 ? 1 : -1;
        twists.push(twist);
        tops.push({
          x: pts[i].x + normals[i].nx * w * sign,
          y: pts[i].y + normals[i].ny * w * sign,
        });
        bots.push({
          x: pts[i].x - normals[i].nx * w * sign,
          y: pts[i].y - normals[i].ny * w * sign,
        });
      }
      return { tops, bots, twists };
    }

    function getFoldColor(frac: number, time: number, isDark: boolean) {
      const cycle =
        (((frac * COLOR_CYCLE_FREQ + time * COLOR_CYCLE_SPEED) % 1) + 1) % 1;
      
      const colorA = isDark ? D_COLOR_FOLD_A : L_COLOR_FOLD_A;
      const colorB = isDark ? D_COLOR_FOLD_B : L_COLOR_FOLD_B;
      const colorC = isDark ? D_COLOR_FOLD_C : L_COLOR_FOLD_C;

      if (cycle < 1 / 3) return lerpColor(colorA, colorB, cycle * 3);
      if (cycle < 2 / 3) return lerpColor(colorB, colorC, (cycle - 1 / 3) * 3);
      return lerpColor(colorC, colorA, (cycle - 2 / 3) * 3);
    }

    function getRibbonColor(frac: number, twist: number, time: number, isDark: boolean) {
      const foldColor = getFoldColor(frac, time, isDark);
      const faceColor = isDark ? D_COLOR_FACE : L_COLOR_FACE;
      const facedness = Math.pow(Math.abs(twist), FACE_BLEND_GAMMA);
      return lerpColor(foldColor, faceColor, facedness);
    }

    function drawQuad(
      ax: number,
      ay: number,
      bx: number,
      by: number,
      cx: number,
      cy: number,
      dx: number,
      dy: number
    ) {
      ctx!.beginPath();
      ctx!.moveTo(ax, ay);
      ctx!.lineTo(bx, by);
      ctx!.lineTo(cx, cy);
      ctx!.lineTo(dx, dy);
      ctx!.closePath();
      ctx!.fill();
    }

    function drawShadow(
      tops: { x: number; y: number }[],
      bots: { x: number; y: number }[],
      isDark: boolean
    ) {
      const color = isDark ? D_SHADOW_COLOR : L_SHADOW_COLOR;
      const alpha = isDark ? D_SHADOW_ALPHA : L_SHADOW_ALPHA;
      ctx!.fillStyle = `rgba(${color[0]}, ${color[1]}, ${color[2]}, ${alpha})`;
      for (let i = 0; i < segments; i++) {
        drawQuad(
          tops[i].x + SHADOW_OFFSET_X,
          tops[i].y + SHADOW_OFFSET_Y,
          tops[i + 1].x + SHADOW_OFFSET_X,
          tops[i + 1].y + SHADOW_OFFSET_Y,
          bots[i + 1].x + SHADOW_OFFSET_X,
          bots[i + 1].y + SHADOW_OFFSET_Y,
          bots[i].x + SHADOW_OFFSET_X,
          bots[i].y + SHADOW_OFFSET_Y
        );
      }
    }

    function drawRibbon(
      tops: { x: number; y: number }[],
      bots: { x: number; y: number }[],
      twists: number[],
      time: number,
      isDark: boolean
    ) {
      const edgeColor = isDark ? D_EDGE_COLOR : L_EDGE_COLOR;
      const edgeAlpha = isDark ? D_EDGE_ALPHA : L_EDGE_ALPHA;

      for (let i = 0; i < segments; i++) {
        const [r, g, b] = getRibbonColor(i / segments, twists[i], time, isDark);
        ctx!.fillStyle = `rgb(${r}, ${g}, ${b})`;
        drawQuad(
          tops[i].x,
          tops[i].y,
          tops[i + 1].x,
          tops[i + 1].y,
          bots[i + 1].x,
          bots[i + 1].y,
          bots[i].x,
          bots[i].y
        );

        if (Math.abs(twists[i]) > EDGE_MIN_TWIST) {
          ctx!.strokeStyle = `rgba(${edgeColor[0]}, ${edgeColor[1]}, ${edgeColor[2]}, ${edgeAlpha})`;
          ctx!.lineWidth = EDGE_WEIGHT;
          ctx!.beginPath();
          ctx!.moveTo(tops[i].x, tops[i].y);
          ctx!.lineTo(tops[i + 1].x, tops[i + 1].y);
          ctx!.stroke();

          ctx!.beginPath();
          ctx!.moveTo(bots[i].x, bots[i].y);
          ctx!.lineTo(bots[i + 1].x, bots[i + 1].y);
          ctx!.stroke();
        }
      }
    }

    function render() {
      ctx!.clearRect(0, 0, canvas!.width, canvas!.height);
      t += waveSpeed;

      // Detect dark mode from the document element
      const isDark = document.documentElement.classList.contains("dark");

      const pts = buildSpine(t);
      const normals = buildNormals(pts);
      const { tops, bots, twists } = buildEdges(pts, normals, t);

      drawShadow(tops, bots, isDark);
      drawRibbon(tops, bots, twists, t, isDark);

      animationFrameId = requestAnimationFrame(render);
    }

    render();

    return () => {
      window.removeEventListener("resize", resize);
      cancelAnimationFrame(animationFrameId);
    };
  }, [segments, waveSpeed, waveAmplitude, twistCycles, lightColors, darkColors]);

  return (
    <div
      ref={containerRef}
      className={cn(
        "relative w-full h-full overflow-hidden rounded-[12px]",
        className
      )}
      {...props}
    >
      <canvas ref={canvasRef} className="absolute inset-0 block w-full h-full" />
    </div>
  );
}

export default TwistingRibbon;

```

---

## `src\components\ui\typing-keyboard.tsx`

```tsx
"use client";

import React, { useEffect, useRef } from "react";
import { cn } from "@/lib/utils";

// ─── Types ──────────────────────────────────────────────────────────────────

export interface TypingKeyboardProps extends React.HTMLAttributes<HTMLDivElement> {
  /** Text to auto-type in a loop */
  autoTypeText?: string;
  /** [min, max] ms delay between keystrokes */
  typingSpeed?: [number, number];
  /** Overall scale factor */
  scale?: number;
  /** Accent color (modifier keys + screen glow) */
  accentColor?: string;
  /** Secondary accent (enter key) */
  secondaryAccent?: string;
}

// ─── Key sub-component ──────────────────────────────────────────────────────

function Key({ size = "", accent = "" }: { size?: string; accent?: string }) {
  const s = size ? `--${size}` : "";
  const a1 = accent === "b" ? " tk-face--b" : accent === "o" ? " tk-face--o" : "";

  return (
    <div className={`tk-key tk-flex${size ? ` tk-key${s}` : ""}`}>
      <div className={`tk-key__front tk-face${size ? ` tk-key__front${s}` : ""}${a1 === " tk-face--b" ? " tk-face--b3" : a1 === " tk-face--o" ? " tk-face--o3" : ""}`} />
      <div className={`tk-key__back tk-face${size ? ` tk-key__back${s}` : ""}${a1 === " tk-face--b" ? " tk-face--b1" : a1 === " tk-face--o" ? " tk-face--o1" : ""}`} />
      <div className={`tk-key__right tk-face${size ? ` tk-key__right${s}` : ""}${a1 === " tk-face--b" ? " tk-face--b1" : a1 === " tk-face--o" ? " tk-face--o1" : ""}`} />
      <div className={`tk-key__left tk-face${size ? ` tk-key__left${s}` : ""}${a1 === " tk-face--b" ? " tk-face--b2" : a1 === " tk-face--o" ? " tk-face--o2" : ""}`} />
      <div className={`tk-key__top tk-face${size ? ` tk-key__top${s}` : ""}${a1 === " tk-face--b" ? " tk-face--b1" : a1 === " tk-face--o" ? " tk-face--o1" : ""}`} />
      <div className={`tk-key__bottom tk-face${size ? ` tk-key__bottom${s}` : ""}${a1 === " tk-face--b" ? " tk-face--b2" : a1 === " tk-face--o" ? " tk-face--o2" : ""}`} />
    </div>
  );
}

// ─── Keycode → DOM index map ────────────────────────────────────────────────

const KC_MAP: Record<number, number> = {
  81:15, 87:16, 69:17, 82:18, 84:19, 89:20, 85:21, 73:22, 79:23, 80:24,
  65:29, 83:30, 68:31, 70:32, 71:33, 72:34, 74:35, 75:36, 76:37,
  90:41, 88:42, 67:43, 86:44, 66:45, 78:46, 77:47,
  32:56, 13:39, 8:27,
};

// ─── Main Component ─────────────────────────────────────────────────────────

export function TypingKeyboard({
  className,
  autoTypeText = "Draco. Fast, private, and beautiful web browser. Built with love by one stubborn developer.       ",
  typingSpeed = [40, 120],
  scale = 0.8,
  accentColor = "#3b82f6",
  secondaryAccent = "#a855f7",
  ...props
}: TypingKeyboardProps) {
  const mainRef = useRef<HTMLDivElement>(null);
  const kbRef = useRef<HTMLDivElement>(null);
  const screenRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    const kb = kbRef.current;
    const screen = screenRef.current;
    if (!kb || !screen) return;

    // Set fixed isometric perspective
    kb.style.transform = "perspective(10000px) rotateX(60deg) rotateZ(-35deg)";

    const allKeys = kb.querySelectorAll<HTMLDivElement>(".tk-key");
    let alive = true;
    let idx = 0;
    let timer: ReturnType<typeof setTimeout>;

    const pressKey = (kc: number) => {
      const domIdx = KC_MAP[kc];
      const el = allKeys[domIdx];
      if (el) {
        el.classList.add("tk-key--down");
        setTimeout(() => el.classList.remove("tk-key--down"), 80);
      }
    };

    const typeNext = () => {
      if (!alive || !screen) return;
      const char = autoTypeText[idx];
      const kc = char === " " ? 32 : char.toUpperCase().charCodeAt(0);

      pressKey(kc);
      screen.innerHTML += char === " " ? " " : char;

      idx++;
      if (idx >= autoTypeText.length) {
        timer = setTimeout(() => {
          if (!alive) return;
          screen.innerHTML = "";
          idx = 0;
          timer = setTimeout(typeNext, 1000);
        }, 2000);
      } else {
        const delay = typingSpeed[0] + Math.random() * (typingSpeed[1] - typingSpeed[0]);
        timer = setTimeout(typeNext, delay);
      }
    };

    timer = setTimeout(typeNext, 1500);
    return () => { alive = false; clearTimeout(timer); };
  }, [autoTypeText, typingSpeed]);

  return (
    <div className={cn("tk-container", className)} {...props}>
      <style>{`
        .tk-container * { transform-style: preserve-3d; }
        .tk-container {
          width: 100%; height: 100%;
          display: flex; justify-content: center; align-items: center;
          font-family: sans-serif; font-weight: bolder;
          color: rgba(255,255,251,0.7);
          text-transform: uppercase; letter-spacing: 2px;
        }
        .tk-main {
          width: 800px; height: 600px;
          position: relative; cursor: pointer;
          transform: scale(${scale});
          transform-origin: center center;
        }
        .tk-flex { display: flex; justify-content: center; align-items: center; }
        .tk-face { position: absolute; }

        /* Keyboard body */
        .tk-keyboard {
          width: 500px; height: 160px;
          transform: perspective(10000px) rotateX(50deg) rotateZ(-25deg);
        }
        .tk-keyboard__front { width: 500px; height: 25px; transform: rotateX(-90deg) translateZ(80px); background-color: #9C9C9C; }
        .tk-keyboard__back  { width: 500px; height: 25px; transform: rotateX(90deg) translateZ(80px); background-color: #FFFFFB; }
        .tk-keyboard__top   {
          display: flex; flex-direction: column; justify-content: space-around;
          width: 500px; height: 160px;
          transform: rotateY(0deg) translateZ(12.5px);
          background-image: linear-gradient(to bottom, color-mix(in srgb, ${accentColor} 40%, white), color-mix(in srgb, #1a1919 80%, white));
        }
        .tk-keyboard__bottom {
          width: 500px; height: 160px;
          transform: rotateY(180deg) translateZ(12.5px);
          background-color: #EAE7E5;
        }
        .tk-keyboard__right { width: 25px; height: 160px; transform: rotateY(90deg) translateZ(250px); background-color: #FFFFFB; }
        .tk-keyboard__left  { width: 25px; height: 160px; transform: rotateY(90deg) translateZ(-250px); background-color: #EAE7E5; }

        /* Screen */
        .tk-screen {
          width: 303px; height: 240px;
          transform: translateZ(100px) translateY(-200px) translateZ(50px) rotateX(270deg);
          background-color: ${accentColor};
          border-radius: 10px; padding: 16px;
          font-size: 14px; line-height: 1.5;
          word-wrap: break-word; white-space: pre-wrap; overflow: hidden;
          text-transform: none; letter-spacing: 1px; color: #fff;
          align-items: flex-start !important;
          justify-content: flex-start !important;
          box-shadow:
            0 0 5px color-mix(in srgb, ${accentColor} 80%, transparent),
            0 0 10px color-mix(in srgb, ${accentColor} 70%, transparent),
            0 0 15px color-mix(in srgb, ${accentColor} 60%, transparent),
            0 0 20px color-mix(in srgb, ${accentColor} 50%, transparent),
            0 0 40px color-mix(in srgb, ${accentColor} 40%, transparent),
            0 0 60px color-mix(in srgb, ${accentColor} 30%, transparent);
          animation: tk-screen-flicker 1s ease-in alternate infinite;
        }
        @keyframes tk-screen-flicker {
          0%, 90%, 96% { background-color: ${accentColor}; }
          93%, 100%    { background-color: color-mix(in srgb, ${accentColor} 80%, black); }
        }

        /* Keys container */
        .tk-keys {
          display: flex; justify-content: space-between;
          width: 100%;
          transform: translateZ(4px);
          padding: 0 2px;
        }

        /* Individual key — depth is 8px */
        .tk-key { width: 30px; height: 27px; transition: .05s ease; }
        .tk-key--w2 { width: 60px; }
        .tk-key--w3 { width: 90px; }
        .tk-key--w6 { width: 195px; }

        .tk-key__front { width: 30px; height: 8px; transform: rotateX(-90deg) translateZ(13.5px); background-color: #838383; }
        .tk-key__front--w2 { width: 60px; }
        .tk-key__front--w3 { width: 90px; }
        .tk-key__front--w6 { width: 195px; }

        .tk-key__back { width: 30px; height: 8px; transform: rotateX(90deg) translateZ(13.5px); background-color: #FFFFFB; }
        .tk-key__back--w2 { width: 60px; }
        .tk-key__back--w3 { width: 90px; }
        .tk-key__back--w6 { width: 195px; }

        .tk-key__top {
          width: 30px; height: 27px;
          transform: rotateY(0deg) translateZ(4px);
          background-color: #FFFFFB;
          background-image: linear-gradient(to bottom, color-mix(in srgb, ${accentColor} 30%, white), #FFFFFB);
          box-shadow: inset 0 0 0 0.5px rgba(0,0,0,0.12), 0 -1px 2px rgba(0,0,0,0.08);
        }
        .tk-key__top--w2 { width: 60px; }
        .tk-key__top--w3 { width: 90px; }
        .tk-key__top--w6 { width: 195px; }

        .tk-key__bottom { width: 30px; height: 27px; transform: rotateY(180deg) translateZ(4px); background-color: #838383; }
        .tk-key__bottom--w2 { width: 60px; }
        .tk-key__bottom--w3 { width: 90px; }
        .tk-key__bottom--w6 { width: 195px; }

        .tk-key__right { width: 8px; height: 27px; transform: rotateY(90deg) translateZ(15px); background-color: #838383; }
        .tk-key__right--w2 { transform: rotateY(90deg) translateZ(30px); }
        .tk-key__right--w3 { transform: rotateY(90deg) translateZ(45px); }
        .tk-key__right--w6 { transform: rotateY(90deg) translateZ(97.5px); }

        .tk-key__left {
          width: 8px; height: 27px;
          transform: rotateY(90deg) translateZ(-15px);
          background-image: linear-gradient(to bottom, #c4c4c4, #b8b8b8);
        }
        .tk-key__left--w2 { transform: rotateY(90deg) translateZ(-30px); }
        .tk-key__left--w3 { transform: rotateY(90deg) translateZ(-45px); }
        .tk-key__left--w6 { transform: rotateY(90deg) translateZ(-97.5px); }

        /* Accent colors */
        .tk-face--b1 { background: ${accentColor}; }
        .tk-face--b2 { background-image: linear-gradient(to bottom, color-mix(in srgb, ${accentColor} 80%, black), ${accentColor}); }
        .tk-face--b3 { background-color: color-mix(in srgb, ${accentColor} 60%, black); }

        .tk-face--o1 { background: ${secondaryAccent}; }
        .tk-face--o2 { background-image: linear-gradient(to bottom, color-mix(in srgb, ${secondaryAccent} 80%, black), ${secondaryAccent}); }
        .tk-face--o3 { background-color: color-mix(in srgb, ${secondaryAccent} 60%, black); }

        /* Pressed state */
        .tk-key--down {
          display: flex; justify-content: center; align-items: center;
          transform: translateZ(-5px);
          transition: .05s ease;
        }
        .tk-key--down > .tk-key__top {
          background: color-mix(in srgb, ${secondaryAccent} 80%, white) !important;
        }
      `}</style>

      <div className="tk-main tk-flex" ref={mainRef}>
        <div className="tk-keyboard tk-flex" ref={kbRef}>
          <div className="tk-screen tk-flex" ref={screenRef} />

          <div className="tk-keyboard__front tk-face" />
          <div className="tk-keyboard__back tk-face" />
          <div className="tk-keyboard__right tk-face" />
          <div className="tk-keyboard__left tk-face" />
          <div className="tk-keyboard__top tk-face">

            {/* Row 1 */}
            <div className="tk-keys">
              <Key accent="b" />
              {Array.from({ length: 12 }).map((_, i) => <Key key={`r1-${i}`} />)}
              <Key size="w2" accent="b" />
            </div>

            {/* Row 2 */}
            <div className="tk-keys">
              <Key size="w2" accent="b" />
              {Array.from({ length: 12 }).map((_, i) => <Key key={`r2-${i}`} />)}
              <Key accent="b" />
            </div>

            {/* Row 3 */}
            <div className="tk-keys">
              <Key size="w3" accent="b" />
              {Array.from({ length: 10 }).map((_, i) => <Key key={`r3-${i}`} />)}
              <Key size="w2" accent="o" />
            </div>

            {/* Row 4 */}
            <div className="tk-keys">
              <Key size="w2" accent="b" />
              {Array.from({ length: 11 }).map((_, i) => <Key key={`r4-${i}`} />)}
              <Key size="w3" accent="b" />
            </div>

            {/* Row 5 */}
            <div className="tk-keys">
              <Key accent="b" />
              <Key accent="o" />
              {Array.from({ length: 2 }).map((_, i) => <Key key={`r5a-${i}`} accent="b" />)}
              <Key size="w6" />
              {Array.from({ length: 5 }).map((_, i) => <Key key={`r5b-${i}`} accent="b" />)}
            </div>

          </div>
          <div className="tk-keyboard__bottom tk-face" />
        </div>
      </div>
    </div>
  );
}

export default TypingKeyboard;
// trigger vercel build

```

---

## `src\components\ui\verse-cards.tsx`

```tsx
"use client";

import * as React from "react";
import { useEffect, useRef, useState } from "react";
import gsap from "gsap";
import { Layers, X } from "lucide-react";
import { cn } from "@/lib/utils";

/**
 * Verse Cards
 *
 * A dock-style nav whose trigger fans a deck of cards up from below on a
 * staggered `power4.inOut` sweep. Once open, the deck behaves like a stack you
 * flip through: clicking the front card flings it away and the next card slides
 * forward to take its place. The close button (or re-triggering) sends the whole
 * stack back down and resets it.
 *
 * Ported from the vanilla "CodeGrid Verse Cards" (GSAP) into a single,
 * self-contained, prop-driven React component. Icons are passed as nodes, so
 * the only runtime dependency is GSAP (plus lucide-react for the defaults).
 */

export interface VerseNavItem {
  /** Label revealed under the icon on hover. */
  label: string;
  /** Icon node rendered inside the tile. */
  icon: React.ReactNode;
  /** Show the little notification dot in the corner. */
  badge?: boolean;
  /** When true, clicking this item opens/closes the card deck. */
  isTrigger?: boolean;
  /** Click handler for non-trigger items. */
  onClick?: () => void;
}

export interface VerseCard {
  /** Text shown on the card. */
  label: string;
  /** Extra classes for this specific card (e.g. a custom background). */
  className?: string;
}

export interface VerseCardsProps {
  /** Nav tiles. Mark one with `isTrigger` to open the deck. Defaults to a single "Work" trigger. */
  navItems?: VerseNavItem[];
  /** Cards in the deck. Strings are treated as labels. Defaults to a sample set. */
  cards?: (VerseCard | string)[];
  /** Caption shown under the open deck. */
  footerText?: string;
  /** Called whenever the deck opens (true) or closes (false). */
  onOpenChange?: (open: boolean) => void;
  /** Called when the front card is flicked away, with its index in the deck. */
  onDeal?: (index: number) => void;
  /** Extra classes for the root element. */
  className?: string;
}

const DEFAULT_NAV: VerseNavItem[] = [
  { label: "Work", icon: <Layers className="h-5 w-5" />, badge: true, isTrigger: true },
];

const DEFAULT_CARDS: VerseCard[] = [
  { label: "Sonyverse" },
  { label: "Nota" },
  { label: "Blinder" },
  { label: "Cinovas" },
  { label: "Uito" },
];

/** Resting transform for a card sitting at `depth` in the stack (0 = front). */
function stackTransform(depth: number) {
  return { x: depth * 12, y: depth * 8, rotation: depth * 3, opacity: 1 };
}

/** Transform for a card that's been dealt away — flung up and off to a side. */
function awayTransform(index: number) {
  const side = index % 2 === 0 ? -1 : 1;
  return { x: side * 460, y: -540, rotation: side * 34, opacity: 0 };
}

/** Where every card sits below the stage before it's revealed. */
const CLOSED = { x: 0, y: 1000, rotation: 0, opacity: 1 };

export function VerseCards({
  navItems = DEFAULT_NAV,
  cards = DEFAULT_CARDS,
  footerText = "Click the front card to deal it away.",
  onOpenChange,
  onDeal,
  className,
}: VerseCardsProps) {
  const rootRef = useRef<HTMLDivElement>(null);
  const [open, setOpen] = useState(false);
  const [dealt, setDealt] = useState(0);
  const [ready, setReady] = useState(false);

  const mountedRef = useRef(false);
  const prevOpenRef = useRef(open);

  const onOpenChangeRef = useRef(onOpenChange);
  const onDealRef = useRef(onDeal);
  useEffect(() => {
    onOpenChangeRef.current = onOpenChange;
    onDealRef.current = onDeal;
  }, [onOpenChange, onDeal]);

  const normalizedCards: VerseCard[] = cards.map((c) =>
    typeof c === "string" ? { label: c } : c,
  );
  const cardCount = normalizedCards.length;

  // Drive the whole deck from (open, dealt): closed → below the stage; open →
  // front-to-back stack; dealt cards fling away and the rest slide forward.
  useEffect(() => {
    const root = rootRef.current;
    if (!root) return;

    const cardEls = gsap.utils.toArray<HTMLElement>(root.querySelectorAll("[data-verse-card]"));
    const closeEl = root.querySelector("[data-verse-close]");
    const footerEl = root.querySelector("[data-verse-footer]");

    const target = (i: number) => {
      if (!open) return CLOSED;
      if (i < dealt) return awayTransform(i);
      return stackTransform(i - dealt);
    };

    if (!mountedRef.current) {
      // Seat everything in its closed state without animating, then reveal the
      // stage so the first paint never flashes the cards in view.
      cardEls.forEach((el, i) => gsap.set(el, target(i)));
      if (closeEl) gsap.set(closeEl, { scale: 0 });
      if (footerEl) gsap.set(footerEl, { opacity: 0 });
      mountedRef.current = true;
      prevOpenRef.current = open;
      setReady(true);
      return;
    }

    const isToggle = prevOpenRef.current !== open;
    prevOpenRef.current = open;

    gsap.to(cardEls, {
      x: (i) => target(i).x,
      y: (i) => target(i).y,
      rotation: (i) => target(i).rotation,
      opacity: (i) => target(i).opacity,
      duration: isToggle ? 0.9 : 0.6,
      ease: isToggle ? "power4.inOut" : "power3.out",
      stagger: isToggle ? { amount: 0.3, from: open ? "start" : "end" } : 0,
      overwrite: "auto",
    });

    if (closeEl) {
      gsap.to(closeEl, {
        scale: open ? 1 : 0,
        duration: 0.4,
        delay: isToggle && open ? 0.5 : 0,
        ease: "back.out(1.7)",
        overwrite: "auto",
      });
    }
    if (footerEl) {
      gsap.to(footerEl, { opacity: open ? 1 : 0, duration: 0.4, overwrite: "auto" });
    }
  }, [open, dealt, cardCount]);

  const openDeck = () => {
    setDealt(0);
    setOpen(true);
    onOpenChangeRef.current?.(true);
  };

  const closeDeck = () => {
    setOpen(false);
    setDealt(0);
    onOpenChangeRef.current?.(false);
  };

  const toggle = () => (open ? closeDeck() : openDeck());

  const dealFront = (index: number) => {
    if (!open || index !== dealt || dealt >= cardCount) return;
    onDealRef.current?.(index);
    setDealt((d) => d + 1);
  };

  return (
    <div
      ref={rootRef}
      className={cn(
        "relative flex h-full w-full items-center justify-center overflow-hidden bg-neutral-100 dark:bg-[#0c0c0c]",
        className,
      )}
    >
      {/* Nav dock */}
      <div className="flex gap-6">
        {navItems.map((item, i) => (
          <div
            key={`${item.label}-${i}`}
            role="button"
            tabIndex={0}
            aria-label={item.label}
            aria-expanded={item.isTrigger ? open : undefined}
            onClick={item.isTrigger ? toggle : item.onClick}
            onKeyDown={(e) => {
              if (e.key === "Enter" || e.key === " ") {
                e.preventDefault();
                if (item.isTrigger) toggle();
                else item.onClick?.();
              }
            }}
            className="group relative flex cursor-pointer flex-col items-center gap-2.5"
          >
            <div className="relative flex h-[50px] w-[50px] items-center justify-center rounded-[10px] border border-neutral-300 bg-gradient-to-b from-white to-neutral-200 text-neutral-800 dark:border-neutral-700 dark:from-neutral-800 dark:to-neutral-900 dark:text-white">
              {item.icon}
              {item.badge ? (
                <span className="absolute -right-1 -top-1 h-2.5 w-2.5 rounded-full bg-neutral-900 dark:bg-white" />
              ) : null}
            </div>
            <span className="translate-y-2.5 text-xs font-medium tracking-wide text-neutral-600 opacity-0 transition-all duration-300 group-hover:translate-y-0 group-hover:opacity-100 dark:text-neutral-400">
              {item.label}
            </span>
          </div>
        ))}
      </div>

      {/* Card deck overlay */}
      <div
        style={{ pointerEvents: open ? "auto" : "none" }}
        className={cn("absolute inset-0", !ready && "invisible")}
      >
        <button
          type="button"
          data-verse-close
          onClick={closeDeck}
          aria-label="Close cards"
          className="absolute left-1/2 top-[6%] z-[200] flex h-12 w-12 -translate-x-1/2 items-center justify-center rounded-full bg-neutral-900 text-white shadow-lg transition-transform hover:scale-105 active:scale-95 dark:bg-white dark:text-neutral-900"
        >
          <X className="h-5 w-5" />
        </button>

        {normalizedCards.map((card, i) => {
          const isFront = open && i === dealt;
          return (
            <div
              key={`${card.label}-${i}`}
              data-verse-card
              onClick={() => dealFront(i)}
              style={{
                zIndex: i < dealt ? 0 : 100 - (i - dealt),
                pointerEvents: isFront ? "auto" : "none",
                cursor: isFront ? "pointer" : "default",
              }}
              className={cn(
                "absolute left-1/2 top-1/2 flex h-[300px] w-[200px] -translate-x-1/2 -translate-y-1/2 select-none items-center justify-center rounded-[10px] bg-white text-[17px] font-medium tracking-tight text-neutral-800 shadow-[0_0_50px_10px_rgba(0,0,0,0.2)] dark:bg-neutral-200 dark:text-neutral-900",
                card.className,
              )}
            >
              {card.label}
            </div>
          );
        })}

        <div
          data-verse-footer
          className="absolute bottom-[6%] left-1/2 z-[200] -translate-x-1/2 text-sm font-medium tracking-tight text-neutral-500"
        >
          {dealt >= cardCount ? "That's the whole deck — close to reset." : footerText}
        </div>
      </div>
    </div>
  );
}

export default VerseCards;

```

---

## `src\components\ui\wave-grid-background.tsx`

```tsx
"use client";

import * as React from "react";
import { useEffect, useRef } from "react";
import * as THREE from "three";
import { EffectComposer } from "three/addons/postprocessing/EffectComposer.js";
import { RenderPass } from "three/addons/postprocessing/RenderPass.js";
import { ShaderPass } from "three/addons/postprocessing/ShaderPass.js";
import { OutputPass } from "three/addons/postprocessing/OutputPass.js";
import { cn } from "@/lib/utils";

/**
 * Wave Grid Background
 * An interactive 40×40 grid of instanced cubes that ripple with fluid,
 * wave-like motion in response to the cursor (falling back to gentle
 * auto-generated ripples when idle). Built with Three.js + custom GLSL,
 * a mouse-trail displacement shader, and a vignette / RGB-shift post pass.
 *
 * Ported from the vanilla "3D Wave Grid" (Three.js) into a single,
 * self-contained, prop-driven React component.
 */

const MAX_TRAIL = 128;

const VIGNETTE_RGB_SHIFT_SHADER = {
  uniforms: {
    tDiffuse: { value: null as THREE.Texture | null },
    shiftAmount: { value: 0.005 },
    vignetteRadius: { value: 0.3 },
    vignetteSoftness: { value: 0.3 },
  },
  vertexShader: /* glsl */ `
    varying vec2 vUv;
    void main() {
      vUv = uv;
      gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
    }
  `,
  fragmentShader: /* glsl */ `
    uniform sampler2D tDiffuse;
    uniform float shiftAmount;
    uniform float vignetteRadius;
    uniform float vignetteSoftness;
    varying vec2 vUv;

    void main() {
      vec2 center = vec2(0.5);
      float dist = distance(vUv, center);
      float horzQuadrant = sign(vUv.x - center.x);
      float vertQuadrant = sign(vUv.y - center.y);

      float vignetteFactor = smoothstep(vignetteRadius, vignetteRadius + vignetteSoftness, dist);
      float currentShift = shiftAmount * vignetteFactor;

      float r = texture2D(tDiffuse, vUv + vec2(currentShift * horzQuadrant, currentShift * vertQuadrant)).r;
      float g = texture2D(tDiffuse, vUv).g;
      float b = texture2D(tDiffuse, vUv - vec2(currentShift * horzQuadrant, currentShift * vertQuadrant)).b;

      float darken = 1.0 - vignetteFactor * 0.5;
      gl_FragColor = vec4(vec3(r, g, b) * darken, 1.0);
    }
  `,
};

// Injects the mouse-trail wave displacement into a standard material's vertex
// shader. Shared between the visible material and the depth material so the
// shadows follow the same deformation.
function overrideVertexShader(vertexShader: string): string {
  return vertexShader
    .replace(
      "#include <common>",
      /* glsl */ `#include <common>
      varying float vHeight;
      attribute vec2 aOffset;
      uniform sampler2D uTrailTexture;
      uniform int       uTrailCount;
      uniform float     uWaveSpeed;
      uniform float     uWaveFreq;
      uniform float     uWaveWidth;
      uniform float     uFadeTime;
      uniform float     uAmplitude;
      uniform float     uJitter;
      uniform float     uMaxHeight;

      vec2 hash2( vec2 p ) {
        p = vec2( dot( p, vec2( 127.1, 311.7 ) ), dot( p, vec2( 269.5, 183.3 ) ) );
        return fract( sin( p ) * 43758.5453123 ) - 0.5;
      }`,
    )
    .replace(
      "#include <begin_vertex>",
      /* glsl */ `#include <begin_vertex>

      vHeight = 0.0;

      if ( position.y > 0.0 ) {
        vec2 jitter  = hash2( aOffset ) * uJitter;
        vec2 worldXZ = aOffset + jitter;
        float waveHeight  = 0.0;
        float totalWeight = 0.0;

        for ( int i = 0; i < uTrailCount; i++ ) {
          vec4 td = texture2D( uTrailTexture, vec2( ( float(i) + 0.5 ) / 128.0, 0.5 ) );
          float dist      = length( worldXZ - td.rg );
          float wavefront = uWaveSpeed * td.b;
          float relDist   = dist - wavefront;

          float window = exp( -( relDist * relDist ) / ( uWaveWidth * uWaveWidth ) );
          float fade   = exp( -td.b / uFadeTime );
          float atten  = 1.0 / ( 1.0 + dist * 0.1 );
          float weight = fade * window * atten * td.a;

          waveHeight  += weight * cos( uWaveFreq * relDist );
          totalWeight += weight;
        }

        waveHeight /= max( totalWeight, 1.0 );

        float displacement = clamp( waveHeight * uAmplitude, -uMaxHeight, uMaxHeight );
        transformed.y += displacement;
        vHeight = displacement;
      }`,
    );
}

export interface WaveGridBackgroundProps {
  /** Content rendered on top of the animated background. */
  children?: React.ReactNode;
  /** Extra classes for the wrapper element. */
  className?: string;
  /** Grid resolution (N×N cubes). Defaults to 40. */
  gridSize?: number;
  /** Base cube color / scene tint. Defaults to white. */
  colorBase?: string;
  /** Color of the wave peaks. Defaults to blue. */
  colorHigh?: string;
  /** Peak displacement multiplier. Defaults to 0.4. */
  waveAmplitude?: number;
  /** Wavefront expansion speed (world units/sec). Defaults to 6. */
  waveSpeed?: number;
  /** Spatial oscillation frequency. Defaults to 1.2. */
  waveFrequency?: number;
  /** Gaussian half-width of the wave ring. Defaults to 3. */
  waveWidth?: number;
  /** Hard clamp on displacement height. Defaults to 0.4. */
  waveMaxHeight?: number;
  /** Per-cube positional jitter. Defaults to 0.2. */
  waveJitter?: number;
  /** Emit gentle random ripples while the cursor is idle. Defaults to true. */
  autoAnimate?: boolean;
  /** Apply the vignette + RGB-shift post-processing pass. Defaults to true. */
  vignette?: boolean;
}

export function WaveGridBackground({
  children,
  className,
  gridSize = 40,
  colorBase = "#ffffff",
  colorHigh = "#0055ff",
  waveAmplitude = 0.4,
  waveSpeed = 6.0,
  waveFrequency = 1.2,
  waveWidth = 3.0,
  waveMaxHeight = 0.4,
  waveJitter = 0.2,
  autoAnimate = true,
  vignette = true,
}: WaveGridBackgroundProps) {
  const containerRef = useRef<HTMLDivElement>(null);
  const canvasRef = useRef<HTMLCanvasElement>(null);

  // Keep the latest prop values available to the (once-created) scene without
  // tearing it down on every change.
  const propsRef = useRef({
    colorBase,
    colorHigh,
    waveAmplitude,
    waveSpeed,
    waveFrequency,
    waveWidth,
    waveMaxHeight,
    waveJitter,
    autoAnimate,
  });
  useEffect(() => {
    propsRef.current = {
      colorBase,
      colorHigh,
      waveAmplitude,
      waveSpeed,
      waveFrequency,
      waveWidth,
      waveMaxHeight,
      waveJitter,
      autoAnimate,
    };
  });

  useEffect(() => {
    const container = containerRef.current;
    const canvas = canvasRef.current;
    if (!container || !canvas) return;

    const cubeWidth = 0.8;
    const cubeHeight = 3;
    const gap = 0.01;
    const bounds = gridSize * (cubeWidth + gap);

    // ── Sizes ────────────────────────────────────────────────────────────────
    const getSize = () => ({
      width: container.clientWidth || 1,
      height: container.clientHeight || 1,
      pixelRatio: Math.min(window.devicePixelRatio, 2),
    });
    let size = getSize();

    // ── Scene ────────────────────────────────────────────────────────────────
    const scene = new THREE.Scene();
    scene.background = new THREE.Color(colorBase).multiplyScalar(0.5);

    // ── Camera (mouse-driven orbit) ────────────────────────────────────────
    const radius = 12;
    const alphaRange = Math.PI * 0.03;
    const betaRange = Math.PI * 0.05;
    const mouse = new THREE.Vector2(0, 0);
    const lerpedMouse = new THREE.Vector2(0, 0);

    const camera = new THREE.PerspectiveCamera(40, size.width / size.height, 0.1, 200);
    const positionCamera = (mx: number, my: number) => {
      const alpha = my * alphaRange;
      const beta = mx * betaRange;
      camera.position.set(
        -radius * Math.cos(alpha) * Math.sin(beta),
        radius * Math.cos(alpha) * Math.cos(beta),
        radius * Math.sin(alpha),
      );
      camera.up.set(0, 0, -1);
      camera.lookAt(0, 0, 0);
    };
    positionCamera(0, 0);
    scene.add(camera);

    const onMouseMove = (e: MouseEvent) => {
      mouse.x = (e.clientX / size.width) * 2 - 1;
      mouse.y = -(e.clientY / size.height) * 2 + 1;
    };
    window.addEventListener("mousemove", onMouseMove);

    // ── Lighting ───────────────────────────────────────────────────────────
    const ambientLight = new THREE.AmbientLight("#ffffff", 0.5);
    scene.add(ambientLight);

    const keyLight = new THREE.DirectionalLight("#ffffff", 4.0);
    keyLight.position.set(-20, 10, 6);
    keyLight.castShadow = true;
    keyLight.shadow.mapSize.set(1024, 1024);
    keyLight.shadow.radius = 6;
    keyLight.shadow.camera.near = 0.1;
    keyLight.shadow.camera.far = 60;
    keyLight.shadow.camera.left = -22;
    keyLight.shadow.camera.right = 22;
    keyLight.shadow.camera.top = 22;
    keyLight.shadow.camera.bottom = -22;
    keyLight.shadow.bias = 0.0001;
    scene.add(keyLight);

    const fillLight = new THREE.DirectionalLight("#ffffff", 1.0);
    fillLight.position.set(10, 5, -3);
    scene.add(fillLight);

    // ── Mouse trail (world-space ripple sources) ───────────────────────────
    const trailData = new Float32Array(MAX_TRAIL * 4);
    const trailTexture = new THREE.DataTexture(
      trailData,
      MAX_TRAIL,
      1,
      THREE.RGBAFormat,
      THREE.FloatType,
    );
    trailTexture.needsUpdate = true;

    const trailUniforms = {
      uTrailTexture: { value: trailTexture },
      uTrailCount: { value: 0 },
      uFadeTime: { value: 2.0 },
      uWaveSpeed: { value: waveSpeed },
      uWaveFreq: { value: waveFrequency },
      uWaveWidth: { value: waveWidth },
      uAmplitude: { value: waveAmplitude },
      uJitter: { value: waveJitter },
      uMaxHeight: { value: waveMaxHeight },
    };
    const colorUniforms = {
      uColorBase: { value: new THREE.Color(colorBase) },
      uColorHigh: { value: new THREE.Color(colorHigh) },
    };

    const trail: { x: number; z: number; age: number; distDelta: number }[] = [];
    let lastPoint: { x: number; z: number } | null = null;
    let timeSinceLastMove = 0;
    let randomPointTimer = 0;
    let placingRandom = true;
    const fadeTime = 2.0;
    const trailSpacing = 0.1;

    const rayPlane = new THREE.Mesh(
      new THREE.PlaneGeometry(bounds, bounds),
      new THREE.MeshBasicMaterial({ side: THREE.DoubleSide, visible: false }),
    );
    rayPlane.rotation.x = -Math.PI / 2;
    rayPlane.updateMatrixWorld(true);

    const raycaster = new THREE.Raycaster();
    const pointerNDC = new THREE.Vector2();
    let rect = canvas.getBoundingClientRect();

    const onPointerMove = (e: PointerEvent) => {
      pointerNDC.set(
        ((e.clientX - rect.left) / rect.width) * 2 - 1,
        -((e.clientY - rect.top) / rect.height) * 2 + 1,
      );
      raycaster.setFromCamera(pointerNDC, camera);
      const hits = raycaster.intersectObject(rayPlane);
      if (hits.length === 0) return;
      const { x, z } = hits[0].point;

      let distDelta = 0;
      if (lastPoint) {
        const dx = x - lastPoint.x;
        const dz = z - lastPoint.z;
        distDelta = Math.sqrt(dx * dx + dz * dz);
        if (distDelta < trailSpacing) return;
      }
      if (trail.length >= MAX_TRAIL) trail.shift();
      trail.push({ x, z, age: 0, distDelta });
      lastPoint = { x, z };
      timeSinceLastMove = 0;
      placingRandom = false;
      randomPointTimer = 0;
    };
    canvas.addEventListener("pointermove", onPointerMove);

    const addRandomPoint = () => {
      const x = (Math.random() * 0.5 - 0.25) * bounds;
      const z = (Math.random() * 0.5 - 0.25) * bounds;
      const distDelta = 0.8 + Math.random() * 0.2;
      if (trail.length >= MAX_TRAIL) trail.shift();
      trail.push({ x, z, age: 0, distDelta });
    };

    const updateTrail = (delta: number) => {
      const expiry = fadeTime * 4;
      for (let i = trail.length - 1; i >= 0; i--) {
        trail[i].age += delta;
        if (trail[i].age > expiry) trail.splice(i, 1);
      }

      timeSinceLastMove += delta;
      if (timeSinceLastMove >= 3.0 && !placingRandom && propsRef.current.autoAnimate) {
        placingRandom = true;
        randomPointTimer = 0;
      }
      if (placingRandom && propsRef.current.autoAnimate) {
        randomPointTimer += delta;
        if (randomPointTimer >= 1.5) {
          addRandomPoint();
          randomPointTimer = 0;
        }
      }

      const count = Math.min(trail.length, MAX_TRAIL);
      if (count > 0 || trailUniforms.uTrailCount.value > 0) {
        for (let i = 0; i < count; i++) {
          const ti = i * 4;
          trailData[ti] = trail[i].x;
          trailData[ti + 1] = trail[i].z;
          trailData[ti + 2] = trail[i].age;
          trailData[ti + 3] = trail[i].distDelta;
        }
        trailTexture.needsUpdate = true;
        trailUniforms.uTrailCount.value = count;
      }
    };

    // ── Grid (instanced cubes with wave shader) ────────────────────────────
    const count = gridSize * gridSize;
    const geometry = new THREE.BoxGeometry(cubeWidth, cubeHeight, cubeWidth);
    const offsetAttribute = new THREE.InstancedBufferAttribute(new Float32Array(count * 2), 2);
    geometry.setAttribute("aOffset", offsetAttribute);

    const material = new THREE.MeshPhongMaterial({ color: 0xffffff });
    material.onBeforeCompile = (shader) => {
      Object.assign(shader.uniforms, trailUniforms, colorUniforms);
      shader.vertexShader = overrideVertexShader(shader.vertexShader);
      shader.fragmentShader = shader.fragmentShader
        .replace(
          "#include <common>",
          `#include <common>
          varying float vHeight;
          uniform vec3  uColorBase;
          uniform vec3  uColorHigh;
          uniform float uMaxHeight;`,
        )
        .replace(
          "#include <color_fragment>",
          `#include <color_fragment>
          float t = clamp( vHeight / uMaxHeight, 0.0, 1.0 );
          diffuseColor.rgb = mix( uColorBase, uColorHigh, t );`,
        );
    };

    const depthMaterial = new THREE.MeshDepthMaterial();
    depthMaterial.onBeforeCompile = (shader) => {
      Object.assign(shader.uniforms, trailUniforms);
      shader.vertexShader = overrideVertexShader(shader.vertexShader);
    };

    const instancedMesh = new THREE.InstancedMesh(geometry, material, count);
    instancedMesh.customDepthMaterial = depthMaterial;
    instancedMesh.castShadow = true;
    instancedMesh.receiveShadow = true;
    scene.add(instancedMesh);

    const dummy = new THREE.Object3D();
    const spacing = cubeWidth + gap;
    const offset = ((gridSize - 1) * spacing) / 2;
    for (let i = 0; i < gridSize; i++) {
      for (let j = 0; j < gridSize; j++) {
        const index = i * gridSize + j;
        const x = i * spacing - offset;
        const z = j * spacing - offset;
        dummy.position.set(x, 0, z);
        dummy.updateMatrix();
        instancedMesh.setMatrixAt(index, dummy.matrix);
        offsetAttribute.setXY(index, x, z);
      }
    }
    instancedMesh.instanceMatrix.needsUpdate = true;
    offsetAttribute.needsUpdate = true;

    // ── Renderer + post-processing ─────────────────────────────────────────
    const renderer = new THREE.WebGLRenderer({ canvas, antialias: true });
    renderer.toneMapping = THREE.ACESFilmicToneMapping;
    renderer.toneMappingExposure = 1.95;
    renderer.shadowMap.enabled = true;
    renderer.shadowMap.type = THREE.PCFShadowMap;
    renderer.setClearColor("#808080");
    renderer.setSize(size.width, size.height);
    renderer.setPixelRatio(size.pixelRatio);

    const composer = new EffectComposer(renderer);
    composer.addPass(new RenderPass(scene, camera));
    const vignettePass = new ShaderPass(VIGNETTE_RGB_SHIFT_SHADER);
    if (vignette) composer.addPass(vignettePass);
    composer.addPass(new OutputPass());
    composer.setSize(size.width, size.height);
    composer.setPixelRatio(size.pixelRatio);

    // ── Resize ──────────────────────────────────────────────────────────────
    const applySize = () => {
      size = getSize();
      camera.aspect = size.width / size.height;
      camera.updateProjectionMatrix();
      renderer.setSize(size.width, size.height);
      renderer.setPixelRatio(size.pixelRatio);
      composer.setSize(size.width, size.height);
      composer.setPixelRatio(size.pixelRatio);
      rect = canvas.getBoundingClientRect();
    };
    const resizeObserver = new ResizeObserver(applySize);
    resizeObserver.observe(container);
    window.addEventListener("resize", applySize);

    // ── Animation loop ─────────────────────────────────────────────────────
    const clock = new THREE.Clock();
    renderer.setAnimationLoop(() => {
      const delta = clock.getDelta();
      const p = propsRef.current;

      // Reflect live prop changes into the shader uniforms.
      trailUniforms.uWaveSpeed.value = p.waveSpeed;
      trailUniforms.uWaveFreq.value = p.waveFrequency;
      trailUniforms.uWaveWidth.value = p.waveWidth;
      trailUniforms.uAmplitude.value = p.waveAmplitude;
      trailUniforms.uJitter.value = p.waveJitter;
      trailUniforms.uMaxHeight.value = p.waveMaxHeight;
      colorUniforms.uColorBase.value.set(p.colorBase);
      colorUniforms.uColorHigh.value.set(p.colorHigh);
      scene.background = new THREE.Color(p.colorBase).multiplyScalar(0.5);

      updateTrail(delta);
      lerpedMouse.x += (mouse.x - lerpedMouse.x) * 0.04;
      lerpedMouse.y += (mouse.y - lerpedMouse.y) * 0.04;
      positionCamera(lerpedMouse.x, lerpedMouse.y);
      composer.render();
    });

    // ── Cleanup ──────────────────────────────────────────────────────────────
    return () => {
      renderer.setAnimationLoop(null);
      window.removeEventListener("mousemove", onMouseMove);
      window.removeEventListener("resize", applySize);
      canvas.removeEventListener("pointermove", onPointerMove);
      resizeObserver.disconnect();

      geometry.dispose();
      material.dispose();
      depthMaterial.dispose();
      rayPlane.geometry.dispose();
      (rayPlane.material as THREE.Material).dispose();
      trailTexture.dispose();
      composer.dispose();
      renderer.dispose();
    };
    // Scene is built once; live values flow through propsRef. Rebuild only when
    // structural inputs change.
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [gridSize, vignette]);

  return (
    <div ref={containerRef} className={cn("relative h-full w-full overflow-hidden", className)}>
      <canvas ref={canvasRef} className="block h-full w-full" />
      {children != null && <div className="absolute inset-0">{children}</div>}
    </div>
  );
}

export default WaveGridBackground;

```

---


**Total components extracted: 132**
