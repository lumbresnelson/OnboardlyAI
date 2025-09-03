# OnboardlyAI
Onboardly AI is a multi-tenant SaaS platform for employee and vendor onboarding. Built with the Internet Computer (ICP), Motoko, and React, it streamlines registration, approvals, document handling, and subscription management with enterprise-ready features like audit trails, automated reminders, white-label branding, and tiered billing.

Core Features

Multi-Tenant Architecture: Company-isolated data, principal-based authentication (Internet Identity).
RBAC (Role-Based Access Control): Company Admin, Approver, and Vendor roles.
Vendor & Employee Onboarding: Public forms, document uploads, approval workflows.
Subscription Management: Stripe-powered billing, free trials, tiered pricing (Starter, Growth, Enterprise).
Feature Gating: Usage limits and paywall logic per subscription tier.
Audit Logging: Immutable logs of actions (registrations, approvals, uploads).
Automated Reminders: Configurable email notifications for pending tasks/documents.
White-Label Branding: Logo, color, favicon customization.
Secure Document Handling: Encrypted uploads, restricted downloads.
Analytics & Reporting: Usage, onboarding progress, vendor statistics.
Integrations: Slack/Teams notifications, API + webhooks (Enterprise).

Tech Stack

Frontend: React + TypeScript + TailwindCSS
Backend: Motoko on the Internet Computer
Auth: Internet Identity (Principal-based)
Payments: Stripe (fallback to PayMongo/Xendit regionally)
State Management: React Query
Animations: Framer Motion
Build/Deploy: dfx + canisters

Requirements – Code (Minimal Scaffold)
dfx.json

{
  "canisters": {
    "backend": {
      "main": "src/backend/main.mo",
      "type": "motoko"
    }
  }
}

backend/main.mo (Motoko skeleton)

import Time "mo:base/Time";
import Principal "mo:base/Principal";
import Array "mo:base/Array";
import Nat "mo:base/Nat";

actor Main {
  public type UserRole = { #Admin; #Approver; #Vendor };

  public type AuditEntry = {
    id: Nat;
    ts: Int;
    actor: Text;
    action: Text;
    details: Text;
  };

  stable var auditLogs : [AuditEntry] = [];
  stable var nextId : Nat = 0;

  public shared({caller}) func recordAudit(action: Text, details: Text) : async AuditEntry {
    let entry : AuditEntry = {
      id = nextId;
      ts = Time.now();
      actor = Principal.toText(caller);
      action;
      details;
    };
    nextId += 1;
    auditLogs := Array.append(auditLogs, [entry]);
    entry
  };

  public query func listAudit() : async [AuditEntry] {
    auditLogs
  };

  // Example placeholder methods
  public shared({caller}) func registerVendor(name: Text) : async Bool {
    ignore await recordAudit("REGISTER_VENDOR", name);
    true
  };

  public shared({caller}) func approveVendor(vendorId: Text) : async Bool {
    ignore await recordAudit("APPROVE_VENDOR", vendorId);
    true
  };
}

frontend/components/AuditLogViewer.tsx

import React, { useEffect, useState } from "react";
import { backend } from "../../declarations/backend";

export default function AuditLogViewer() {
  const [logs, setLogs] = useState<any[]>([]);

  useEffect(() => {
    backend.listAudit().then(setLogs);
  }, []);

  return (
    <div>
      <h2 className="text-xl font-bold mb-3">Audit Logs</h2>
      <ul className="space-y-2">
        {logs.map((log, i) => (
          <li key={i} className="border p-2 rounded">
            <strong>{log.action}</strong> — {log.details}  
            <div className="text-xs text-gray-600">
              {new Date(Number(log.ts) / 1_000_000).toLocaleString()} by {log.actor}
            </div>
          </li>
        ))}
      </ul>
    </div>
  );
}

Next Steps:

Add Stripe/PayMongo integration for subscription tiers.
Connect email notifications (SMTP service or SendGrid).
Expand RBAC and vendor workflows.
Deploy canisters via dfx deploy.
