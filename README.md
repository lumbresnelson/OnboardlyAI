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

# Vendor Onboarding SaaS Application

A comprehensive web application for managing vendor onboarding processes with PRD generation, technical architecture specifications, and complete vendor management capabilities built on the Internet Computer.

## Features

- **Principal-Based Authentication**: Secure authentication using Internet Identity with principal-based access control
- **Role-Based Access Control (RBAC)**: Admin, Manager, and User roles with granular permissions
- **Vendor Management**: Complete vendor lifecycle management with organization-based isolation
- **PRD Generation**: Automated generation of Product Requirements Documents with technical specifications
- **Document Management**: Secure document upload and management with encryption
- **Billing Integration**: Stripe integration for subscription management
- **Audit Logging**: Comprehensive audit trails for all user actions
- **Multi-Tenant Architecture**: Organization-based data isolation and access control

## Architecture

### Backend (Motoko)
- **Principal-Based Security**: All operations use `Actor.caller()` for secure principal identification
- **Access Control**: Role-based permissions enforced at the canister level
- **Stable Memory**: Upgrade-safe data storage patterns
- **Multi-Tenant**: Organization-based data isolation using principal membership

### Frontend (React + TypeScript)
- **Authenticated Calls**: All canister calls use authenticated agents
- **Real-Time Updates**: React Query for efficient data synchronization
- **Responsive Design**: Tailwind CSS for modern, mobile-first UI
- **Type Safety**: Full TypeScript integration with generated Candid bindings

## Principal-Based Authentication

### How It Works

The application uses Internet Computer's built-in authentication system where:

1. **Frontend Authentication**: Users authenticate via Internet Identity or similar providers
2. **Principal Identification**: Each authenticated user has a unique Principal ID
3. **Backend Verification**: All backend methods use `Actor.caller()` to identify the calling principal
4. **Organization Membership**: Principals are mapped to organizations with specific roles
5. **Access Control**: Permissions are enforced based on the caller's principal and role

### Authentication Flow

# Vendor Onboarding SaaS Application

A comprehensive web application for managing vendor onboarding processes with PRD generation, technical architecture specifications, and complete vendor management capabilities built on the Internet Computer.

## Features

- **Principal-Based Authentication**: Secure authentication using Internet Identity with principal-based access control
- **Role-Based Access Control (RBAC)**: Admin, Manager, and User roles with granular permissions
- **Vendor Management**: Complete vendor lifecycle management with organization-based isolation
- **PRD Generation**: Automated generation of Product Requirements Documents with technical specifications
- **Document Management**: Secure document upload and management with encryption
- **Billing Integration**: Stripe integration for subscription management
- **Audit Logging**: Comprehensive audit trails for all user actions
- **Multi-Tenant Architecture**: Organization-based data isolation and access control

## Architecture

### Backend (Motoko)
- **Principal-Based Security**: All operations use `Actor.caller()` for secure principal identification
- **Access Control**: Role-based permissions enforced at the canister level
- **Stable Memory**: Upgrade-safe data storage patterns
- **Multi-Tenant**: Organization-based data isolation using principal membership

### Frontend (React + TypeScript)
- **Authenticated Calls**: All canister calls use authenticated agents
- **Real-Time Updates**: React Query for efficient data synchronization
- **Responsive Design**: Tailwind CSS for modern, mobile-first UI
- **Type Safety**: Full TypeScript integration with generated Candid bindings

## Principal-Based Authentication

### How It Works

The application uses Internet Computer's built-in authentication system where:

1. **Frontend Authentication**: Users authenticate via Internet Identity or similar providers
2. **Principal Identification**: Each authenticated user has a unique Principal ID
3. **Backend Verification**: All backend methods use `Actor.caller()` to identify the calling principal
4. **Organization Membership**: Principals are mapped to organizations with specific roles
5. **Access Control**: Permissions are enforced based on the caller's principal and role

### Authentication Flow

import AccessControl "authorization/access-control";
import Principal "mo:base/Principal";
import OrderedMap "mo:base/OrderedMap";
import Time "mo:base/Time";
import Text "mo:base/Text";
import Iter "mo:base/Iter";
import Stripe "stripe/stripe";
import OutCall "http-outcalls/outcall";
import Debug "mo:base/Debug";
import Registry "blob-storage/registry";
import UserApproval "user-approval/approval";

persistent actor {
    // Initialize the user system state
    let accessControlState = AccessControl.initState();

    // Initialize the user approval system state
    let approvalState = UserApproval.initState(accessControlState);

    // Initialize auth (first caller becomes admin, others become users)
    public shared ({ caller }) func initializeAccessControl() : async () {
        AccessControl.initialize(accessControlState, caller);
    };

    public query ({ caller }) func getCallerUserRole() : async AccessControl.UserRole {
        AccessControl.getUserRole(accessControlState, caller);
    };

    public shared ({ caller }) func assignCallerUserRole(user : Principal, role : AccessControl.UserRole) : async () {
        AccessControl.assignRole(accessControlState, caller, user, role);
    };

    public query ({ caller }) func isCallerAdmin() : async Bool {
        AccessControl.isAdmin(accessControlState, caller);
    };

    public type UserProfile = {
        name : Text;
        // Other user metadata if needed
    };

    transient let principalMap = OrderedMap.Make<Principal>(Principal.compare);
    var userProfiles = principalMap.empty<UserProfile>();

    public query ({ caller }) func getCallerUserProfile() : async ?UserProfile {
        principalMap.get(userProfiles, caller);
    };

    public query func getUserProfile(user : Principal) : async ?UserProfile {
        principalMap.get(userProfiles, user);
    };

    public shared ({ caller }) func saveCallerUserProfile(profile : UserProfile) : async () {
        userProfiles := principalMap.put(userProfiles, caller, profile);
    };

    // User Approval System
    public query ({ caller }) func isCallerApproved() : async Bool {
        AccessControl.hasPermission(accessControlState, caller, #admin) or UserApproval.isApproved(approvalState, caller);
    };

    public shared ({ caller }) func setApproval(user : Principal, status : UserApproval.ApprovalStatus) : async () {
        if (not (AccessControl.hasPermission(accessControlState, caller, #admin))) {
            Debug.trap("Unauthorized: Only admins can set approval status");
        };
        UserApproval.setApproval(approvalState, user, status);
    };

    public query ({ caller }) func listApprovals() : async [UserApproval.UserApprovalInfo] {
        if (not (AccessControl.hasPermission(accessControlState, caller, #admin))) {
            Debug.trap("Unauthorized: Only admins can list approvals");
        };
        UserApproval.listApprovals(approvalState);
    };

    // PRD Generator
    public type PrdDocument = {
        title : Text;
        intake : Text;
        prdContent : Text;
        created : Time.Time;
        modified : Time.Time;
    };

    transient let textMap = OrderedMap.Make<Text>(Text.compare);
    var prdDocuments = textMap.empty<PrdDocument>();

    public shared ({ caller }) func generatePrd(title : Text, intake : Text) : async Text {
        if (not (UserApproval.isApproved(approvalState, caller) or AccessControl.hasPermission(accessControlState, caller, #admin))) {
            Debug.trap("Unauthorized: Only approved users can generate PRDs");
        };

        let prdContent = generatePrdContent(intake);
        let now = Time.now();

        let doc : PrdDocument = {
            title = title;
            intake = intake;
            prdContent = prdContent;
            created = now;
            modified = now;
        };

        prdDocuments := textMap.put(prdDocuments, title, doc);
        prdContent;
    };

    public query ({ caller }) func getPrdDocument(title : Text) : async ?PrdDocument {
        if (not (UserApproval.isApproved(approvalState, caller) or AccessControl.hasPermission(accessControlState, caller, #admin))) {
            Debug.trap("Unauthorized: Only approved users can get PRDs");
        };
        textMap.get(prdDocuments, title);
    };

    public query ({ caller }) func listPrdDocuments() : async [(Text, PrdDocument)] {
        if (not (UserApproval.isApproved(approvalState, caller) or AccessControl.hasPermission(accessControlState, caller, #admin))) {
            Debug.trap("Unauthorized: Only approved users can list PRDs");
        };
        Iter.toArray(textMap.entries(prdDocuments));
    };

    public shared ({ caller }) func updatePrdDocument(title : Text, newIntake : Text) : async Text {
        if (not (UserApproval.isApproved(approvalState, caller) or AccessControl.hasPermission(accessControlState, caller, #admin))) {
            Debug.trap("Unauthorized: Only approved users can update PRDs");
        };

        switch (textMap.get(prdDocuments, title)) {
            case null { "Document not found" };
            case (?doc) {
                let updatedDoc : PrdDocument = {
                    title = doc.title;
                    intake = newIntake;
                    prdContent = generatePrdContent(newIntake);
                    created = doc.created;
                    modified = Time.now();
                };
                prdDocuments := textMap.put(prdDocuments, title, updatedDoc);
                updatedDoc.prdContent;
            };
        };
    };

    public shared ({ caller }) func deletePrdDocument(title : Text) : async Text {
        if (not (UserApproval.isApproved(approvalState, caller) or AccessControl.hasPermission(accessControlState, caller, #admin))) {
            Debug.trap("Unauthorized: Only approved users can delete PRDs");
        };

        prdDocuments := textMap.delete(prdDocuments, title);
        "Document deleted";
    };

    // Helper function to generate PRD content
    func generatePrdContent(intake : Text) : Text {
        // In a real implementation, this would parse the intake and generate structured PRD content
        // For this example, we'll just return a template with the intake included
        let prdTemplate = "
# Product Requirements Document

## Intake Information
" # intake # "

## Goals & Non-Goals
- Clearly define what the vendor onboarding SaaS aims to achieve and what is out of scope.

## User Stories
- Given/When/Then format for all user interactions.

## Feature List
- MVP, v1.1, v1.2 features.

## Acceptance Criteria
- Specific criteria for each feature.

## SLA & Performance
- TTFB, p95 latency, uptime targets.

## Analytics Plan
- Events and properties to track.

## Legal/Compliance
- PII handling, data retention, export requirements.
";
        prdTemplate;
    };

    // Stripe integration
    var configuration : ?Stripe.StripeConfiguration = null;

    public query func isStripeConfigured() : async Bool {
        return configuration != null;
    };

    public shared ({ caller }) func setStripeConfiguration(config : Stripe.StripeConfiguration) : async () {
        if (not (AccessControl.hasPermission(accessControlState, caller, #admin))) {
            Debug.trap("Unauthorized: Only admins can set Stripe configuration");
        };
        configuration := ?config;
    };

    private func getStripeConfiguration() : Stripe.StripeConfiguration {
        switch (configuration) {
            case null { { secretKey = ""; allowedCountries = [] } };
            case (?value) value;
        };
    };

    public func getStripeSessionStatus(sessionId : Text) : async Stripe.StripeSessionStatus {
        await Stripe.getSessionStatus(getStripeConfiguration(), sessionId, transform);
    };

    public shared ({ caller }) func createCheckoutSession(items : [Stripe.ShoppingItem], successUrl : Text, cancelUrl : Text) : async Text {
        await Stripe.createCheckoutSession(getStripeConfiguration(), caller, items, successUrl, cancelUrl, transform);
    };

    public query func transform(input : OutCall.TransformationInput) : async OutCall.TransformationOutput {
        OutCall.transform(input);
    };

    // Vendor Management
    public type Vendor = {
        id : Text;
        name : Text;
        organizationId : Text;
        archived : Bool;
    };

    var vendors = textMap.empty<Vendor>();

    public shared ({ caller }) func createVendor(id : Text, name : Text, organizationId : Text) : async Text {
        if (not (UserApproval.isApproved(approvalState, caller) or AccessControl.hasPermission(accessControlState, caller, #admin))) {
            Debug.trap("Unauthorized: Only approved users can create vendors");
        };

        let vendor : Vendor = {
            id = id;
            name = name;
            organizationId = organizationId;
            archived = false;
        };

        vendors := textMap.put(vendors, id, vendor);
        "Vendor created";
    };

    public query ({ caller }) func getVendor(id : Text) : async ?Vendor {
        if (not (UserApproval.isApproved(approvalState, caller) or AccessControl.hasPermission(accessControlState, caller, #admin))) {
            Debug.trap("Unauthorized: Only approved users can get vendors");
        };
        textMap.get(vendors, id);
    };

    public query ({ caller }) func listVendors() : async [(Text, Vendor)] {
        if (not (UserApproval.isApproved(approvalState, caller) or AccessControl.hasPermission(accessControlState, caller, #admin))) {
            Debug.trap("Unauthorized: Only approved users can list vendors");
        };
        Iter.toArray(textMap.entries(vendors));
    };

    public shared ({ caller }) func updateVendor(id : Text, name : Text) : async Text {
        if (not (UserApproval.isApproved(approvalState, caller) or AccessControl.hasPermission(accessControlState, caller, #admin))) {
            Debug.trap("Unauthorized: Only approved users can update vendors");
        };

        switch (textMap.get(vendors, id)) {
            case null { "Vendor not found" };
            case (?vendor) {
                let updatedVendor : Vendor = {
                    vendor with name = name
                };
                vendors := textMap.put(vendors, id, updatedVendor);
                "Vendor updated";
            };
        };
    };

    public shared ({ caller }) func archiveVendor(id : Text) : async Text {
        if (not (UserApproval.isApproved(approvalState, caller) or AccessControl.hasPermission(accessControlState, caller, #admin))) {
            Debug.trap("Unauthorized: Only approved users can archive vendors");
        };

        switch (textMap.get(vendors, id)) {
            case null { "Vendor not found" };
            case (?vendor) {
                let updatedVendor : Vendor = {
                    vendor with archived = true
                };
                vendors := textMap.put(vendors, id, updatedVendor);
                "Vendor archived";
            };
        };
    };

    // Vendor Document Management
    public type VendorDocument = {
        path : Text;
        vendorId : Text;
        filename : Text;
        uploadDate : Time.Time;
        fileSize : Nat;
        documentType : Text;
    };

    var vendorDocuments = textMap.empty<VendorDocument>();
    let registry = Registry.new();

    public shared ({ caller }) func uploadVendorDocument(path : Text, vendorId : Text, filename : Text, fileSize : Nat, documentType : Text) : async Text {
        if (not (UserApproval.isApproved(approvalState, caller) or AccessControl.hasPermission(accessControlState, caller, #admin))) {
            Debug.trap("Unauthorized: Only approved users can upload vendor documents");
        };

        let doc : VendorDocument = {
            path = path;
            vendorId = vendorId;
            filename = filename;
            uploadDate = Time.now();
            fileSize = fileSize;
            documentType = documentType;
        };

        vendorDocuments := textMap.put(vendorDocuments, path, doc);
        Registry.add(registry, path, filename);
        "Document uploaded";
    };

    public query ({ caller }) func getVendorDocuments(vendorId : Text) : async [VendorDocument] {
        if (not (UserApproval.isApproved(approvalState, caller) or AccessControl.hasPermission(accessControlState, caller, #admin))) {
            Debug.trap("Unauthorized: Only approved users can get vendor documents");
        };

        Iter.toArray(
            Iter.filter(
                textMap.vals(vendorDocuments),
                func(doc : VendorDocument) : Bool {
                    doc.vendorId == vendorId;
                },
            )
        );
    };

    public shared ({ caller }) func deleteVendorDocument(path : Text) : async Text {
        if (not (UserApproval.isApproved(approvalState, caller) or AccessControl.hasPermission(accessControlState, caller, #admin))) {
            Debug.trap("Unauthorized: Only approved users can delete vendor documents");
        };

        vendorDocuments := textMap.delete(vendorDocuments, path);
        Registry.remove(registry, path);
        "Document deleted";
    };

    public func registerFileReference(path : Text, hash : Text) : async () {
        Registry.add(registry, path, hash);
    };

    public query func getFileReference(path : Text) : async Registry.FileReference {
        Registry.get(registry, path);
    };

    public query func listFileReferences() : async [Registry.FileReference] {
        Registry.list(registry);
    };

    public func dropFileReference(path : Text) : async () {
        Registry.remove(registry, path);
    };
};


