# Azure Governance
- Governance refers to the ongoing process of managing, monitoring, and auditing your Azure resources and subscriptions
- [Best practices for Azure readiness](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/)

## EA Hierarchy
- [EA Hierarchy](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-setup-guide/organize-resources?tabs=AzureManagementGroupsAndHierarchy)
  - Management Groups
    - Containers that help you manage access, policy, and compliance for multiple subscriptions. 
    - Can set RBAC +  policies at this level to be inherited by subscriptions
  - Subscriptions
    - One Azure Acive Directory can be associated with multiple subscriptions
    - [Tips for creating new subscriptions](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/scale-subscriptions#tips-for-creating-new-subscriptions)
    - [How to Move Resources to a New Resource Group Or Subscription](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/move-resource-group-and-subscription)
    - [Subsciption Limits/Quotas](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/azure-subscription-service-limits)
  - Resource Groups
    - Contains resources
    - Cannot be contained within another resource group
    - The resource group stores metadata about the resources. Therefore, when you specify a location for the resource group, you are specifying where that metadata is stored.
    - Put resources which should be deployed/charged back  together in the same RG. See CAF guidance [here](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/decision-guides/resource-consistency/#basic-grouping)
    - You can apply certain actions on all resources within a group such as deleting an RG, or assign RBAC permissions on an entire group
- [Video: EA Portal  Onboarding Session](https://www.youtube.com/watch?v=OiZ1GdBpo-I)

## Cloud Adoption Framework
- [Cloud Adoption Framework](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/govern/) provides actionable governance guides that represent common experiences often encountered by customers. This framerwork helps you to create personalized governance solutions that meet your business needs.
    - Implement the [Disciplines of Cloud Governance](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/govern/governance-disciplines)
      - Cost Management: Develop policies for cost control for all cloud platforms.
      - Security Baseline: Cloud governance policies and enforcement apply established security requirements across network, data, and asset configurations
      - Identity Baseline: Ensure that identity is consistently applied across cloud adoption efforts.
      - Resource Consistency: Through governance tooling, resources can be configured consistently to manage risks related to onboarding, drift, discoverability, and recovery.
      - Deployment Acceleration: Centralization, standardization, and consistency in approaches to deployment and configuration improve governance practices.
  
### Cost Management
- [Cost Management sample policy statements](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/govern/cost-management/policy-statements)
- Planned versus actual spending can be managed via Azure Cost Management.
- [Azure Advisor](https://docs.microsoft.com/en-us/azure/advisor/advisor-overview) can provide optimization recommendations.

### Security
- [Security Baseline sample policy statements](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/govern/security-baseline/policy-statements#security-review)
- Network activity can be monitored using Azure Network Watcher, and [Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) can help identify security vulnerabilities. 

### Identity
- [Identity Baseline Sample Policy Statements](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/govern/identity-baseline/policy-statements#isolated-identity-providers)
- [Azure Identity Best Practices](https://docs.microsoft.com/en-us/azure/security/fundamentals/identity-management-best-practices)

### Resource Consistency
- [Resource Consistency sample policy statements](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/govern/resource-consistency/policy-statements)
- [Recommended Naming and Tagging Conventions](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)
  - Well-defined naming and metadata tagging conventions help to quickly locate and manage resources
- [Lock resources to prevent unexpected changes]( https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/lock-resources)
  - CanNotDelete means authorized users can still read and modify a resource, but they can't delete the resource.
  - ReadOnly means authorized users can read a resource, but they can't delete or update the resource.
  - When you apply a lock at a parent scope, all resources within that scope inherit the same lock. Even resources you add later inherit the lock from the parent.
  - Consider applying to foundational resources: v-nets, gateways, storage accounts
- [Azure Policy](https://docs.microsoft.com/en-us/azure/governance/policy/overview)
  - Policies and initiatives enforce rules over resources and services in your subscriptions
  - An initiative definition is a collection of policy definitions that are tailored towards achieving a singular overarching goal. They simplify policy management by grouping a set of policies as one single item.
  - [Azure Policy vs RBAC](https://docs.microsoft.com/en-us/azure/governance/policy/overview#azure-policy-and-rbac). Azure Policy evaluates state by examining properties on resources. RBAC focuses on managing user actions at different scopes.
  - [Walkthrough: Create a Policy Assignment to Identify Non-Compliant Resources](https://docs.microsoft.com/en-us/azure/governance/policy/assign-policy-portal)
  - [Policy Effects](https://docs.microsoft.com/en-us/azure/governance/policy/concepts/effects) determine what happens when the policy rule is evaluated to match.
  - [Policy Samples](https://github.com/Azure/azure-policy)
  - [Azure Automation](https://docs.microsoft.com/en-us/azure/automation/automation-intro)
    - Process Automation in Azure Automation allows you to automate frequent, time-consuming, and error-prone cloud management tasks.
    - Configuration Management in Azure Automation allows access to two features: (1) Change Tracking and Inventory (2) Azure Automation State Configuration
    - Update Management allows you to create scheduled deployments that orchestrate the installation of updates within a defined maintenance window.

### Deployment

- Deployment Acceleration sample policy statements https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/govern/deployment-acceleration/policy-statements
- Azure Resource Manager templates enable using infrastructure as code to deploy your resources to Azure. You could also use Terraform as a consistent on-premises and cloud-based deployment tool.
-  Azure Monitor, including Log Analytics and Application Insights, provides tools for collecting and analyzing telemetry to help you understand how your applications are performing and proactively identify issues affecting them and the resources they depend on. Additionally, Azure activity log reports all changes that are being made at the platform level and should be monitored and audited for noncompliant changes.
-  [Azure Blueprints](https://docs.microsoft.com/en-us/azure/governance/blueprints/overview) are a declarative way to orchestrate the deployment of various resource templates and other artifacts such as: (1) Role Assignments (2) Policy Assignments (3) Azure Resource Manager templates (4) Resource Groups
   -  [Overriding Locking States w/ Blueprints](https://docs.microsoft.com/en-us/azure/governance/blueprints/concepts/resource-locking#overriding-locking-states). It's typically possible for someone with appropriate role-based access control (RBAC) on the subscription, such as the 'Owner' role, to be allowed to alter or delete any resource. This access isn't the case when Azure Blueprints applies locking as part of a deployed assignment. If the assignment was set with the Read Only or Do Not Delete option, not even the subscription owner can perform the blocked action on the protected resource.