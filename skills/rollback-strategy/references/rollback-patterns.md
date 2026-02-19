# Rollback Patterns Reference

## Pattern 1: Feature Flag Rollback
- Safest rollback approach
- Disable features without code changes
- LaunchDarkly, Unleash, custom implementation, etc.

## Pattern 2: Blue-Green Deployment
- Switch while keeping the previous version environment intact
- Rollback = traffic switching
- Cost: 2x infrastructure

## Pattern 3: Database Rollback
- Forward-only migration recommended
- Backward-compatible schema changes
  1. Add new column (nullable)
  2. Use new column in code
  3. Deprecate old column
  4. Drop old column after sufficient time

## Pattern 4: Canary Release
- Route only 5–10% of total traffic to the new version
- Gradually expand after monitoring
- Immediately drop to 0% if issues are found
