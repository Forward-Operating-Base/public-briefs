# Buddy Backup Button

**Pillar 5: Health Crisis Response**
**Version**: 0.1.0 (MVP)
**Status**: Phase 4 Nearly Complete (98% MVP)
**Last Updated**: 2025-12-30

A peer-to-clinical crisis escalation system for veterans. Enables helpers (peers) to request backup support through Discord, with consent-based warm handoffs to qualified responders.

## Quick Start

```bash
# Install dependencies
npm install

# Start development server
npm run dev

# Register Discord commands (first time only)
npm run discord:register
```

**Dashboard**: http://localhost:3000/dashboard/responder

## MVP Progress

| Metric | Value |
|--------|-------|
| MVP Tasks | 49/50 (98%) |
| Phase 4 | 8/9 complete |
| Remaining | Messenger consent (optional) |

## Features

### Working (Tested 2025-12-30)
- `/backup` Discord command - Helper initiates backup request
- Bot acknowledgment within 10 seconds (FR-002)
- Responder dashboard with real-time WebSocket updates
- Consent GRANT flow - Veteran approves via DM
- Consent DENY flow - Veteran declines, 988/VCL fallback shown
- Consent REVOKE flow - 625ms revocation time (FR-008: <5s)
- **Approved Context Delivery** - Veteran-consented context displayed to responder

### Context Delivery (NEW - Task 9)
When veteran grants consent, responder receives:
- Situation Summary (helper-provided context)
- Platform Handle (if approved)
- Prior Interaction Count (if applicable)

### Constitutional Compliance
| Principle | Status |
|-----------|--------|
| I. Veteran Agency | Consent controls all responder access |
| II. Peer Support First | Helper initiates, responder joins only with consent |
| III. Consent as Gate | No access without explicit approval |
| IV. Speed Over Sophistication | Simple matching, no ML |
| V. No Surveillance | No message content stored |
| VI. Non-Clinical Boundary | 988/VCL fallback always available |

## Security

### Row Level Security (RLS)

This application implements comprehensive database-level security through PostgreSQL Row Level Security:

- **34 RLS Policies** protecting 10 database tables
- **Consent-based access model** - responders can only access data veterans explicitly approve
- **Privacy-preserving design** - veterans identified by pseudoId until consent granted
- **Defense in depth** - application-level AND database-level access control

See [docs/SECURITY.md](docs/SECURITY.md) for detailed security implementation.

### Key Security Features

- Pseudonymous veteran identities (revealed only with consent)
- Consent as operational security gate
- Audit trail of all access
- Isolation of data between helpers and responders
- Encrypted context sharing based on approved scopes

## Architecture

```
Discord                    Server                    Dashboard
   |                         |                          |
   |--/backup--------------->|                          |
   |<--Acknowledgment--------|                          |
   |                         |--escalation:available--->|
   |                         |<--escalation:accept------|
   |<--Consent DM------------|                          |
   |--Grant/Deny/Revoke----->|                          |
   |                         |--consent:granted-------->|
   |                         |  + contextPackage        |
   |                         |--consent:denied--------->|
   |                         |--consent:revoked-------->|
```

## Project Structure

```
├── src/
│   ├── api/              # Express routes
│   ├── dashboard/        # Responder web dashboard
│   ├── lib/              # Core utilities (db, encryption, jwt)
│   ├── platforms/        # Discord & Messenger integrations
│   ├── realtime/         # Socket.io handlers
│   └── services/         # Business logic
├── demo/                 # Demo documentation & test scripts
├── docs/                 # Implementation status & architecture
├── scripts/              # Discord command registration
└── tests/                # Test files
```

## Environment Variables

Copy `.env.example` to `.env` and configure:
- `DATABASE_URL` - PostgreSQL connection
- `DISCORD_BOT_TOKEN` - Discord bot credentials
- `ENCRYPTION_KEY` - 32-byte hex key for AES-256-GCM
- `JWT_SECRET` - Secret for token signing

## Documentation

- [IMPLEMENTATION_STATUS.md](docs/IMPLEMENTATION_STATUS.md) - Full progress tracking
- [demo/README.md](demo/README.md) - MVP demo documentation
- [demo/QUICK_START.md](demo/QUICK_START.md) - 5-minute demo guide
- [demo/TEST_RESULTS.md](demo/TEST_RESULTS.md) - Detailed test results

## Commands

```bash
npm run dev          # Start development server
npm run build        # Build for production
npm run start        # Start production server
npm run discord:register  # Register Discord slash commands
npm test             # Run tests
```

## Reset for Testing

If responder shows "at capacity":
```bash
npx tsx demo/reset-for-test.ts
```

## Next Steps

1. **Deploy to Railway** (MVP ready)
2. Phase 5: Helper Decision Support scripts
3. Phase 6: Follow-up scheduling
4. Phase 7: Admin compliance dashboard

## License

Proprietary - True North Data Strategies
