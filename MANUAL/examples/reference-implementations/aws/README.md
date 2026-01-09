# AWS Reference Implementation

**AOSD Level 2 Example - AWS Platform**

---

## Purpose

This directory contains sanitized, educational examples of AOSD patterns implemented on AWS. These examples demonstrate how the AOSD Framework (Level 1) can be implemented using AWS services.

**This is a reference implementation** - it shows patterns and approaches but is not meant to be copied directly. Use it as a learning resource when creating your own Orchestration Profile.

---

## What's Included

### Pattern Examples
- **AWS Factory Pattern**: Centralized AWS service client creation (`aws-factory-pattern-example.md`)
- **Lambda Bundling Pattern**: Serverless function packaging
- **Multi-Tenant DynamoDB Pattern**: Data isolation strategies (`multi-tenant-dynamodb-example.md`)
- **WAF Pattern**: API security layer
- **SSM Access Pattern**: Secure environment access

### Environment Strategy
- **AWS Environment Strategy**: Complete environment architecture for AWS (`environment-strategy-aws.md`)

### Architecture Decisions
Example ADRs showing how choices were made for AWS implementation

---

## How to Use These Examples

### For Learning
1. Read pattern examples to understand approaches
2. Study ADRs to see decision-making process
3. Adapt patterns to your own requirements
4. Don't copy-paste - understand and customize

### For Your Own AWS Implementation
1. Start with AOSD Framework principles
2. Use these examples as reference
3. Create your own Orchestration Profile documentation
4. Document your specific choices and rationale

### For Non-AWS Platforms
- Translate concepts to your platform (Azure, GCP, etc.)
- Core AOSD principles remain the same
- Implementation details will differ

---

## Not Included

This reference implementation does NOT include:
- Production code from real projects
- Specific business logic
- Proprietary patterns
- Complete working applications

For complete working examples, see a full Orchestration Profile implementation.

---

## Related Documentation

- [AOSD Framework](../../AOSD_FRAMEWORK.md) - Core methodology
- [Implementation Template](../../IMPLEMENTATION_TEMPLATE.md) - Create your own profile
- [Environment Strategy Principles](../../ENVIRONMENT_STRATEGY.md) - Generalized environment principles
- [Workflow Examples](../workflows/) - AWS + Claude workflow examples
- [AWS Official Documentation](https://docs.aws.amazon.com) - AWS services

---

**Note**: This is a sanitized reference. Real implementations will be more complex and application-specific.
