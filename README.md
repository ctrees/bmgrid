# bmgrid
Baremetal to Render Grid testing

## Goal: Full power up automation of baremetal servers to Blender Render SaaS.

From an isolated 'static-backup' rebuild an entire Blender Render SaaS grid and portal.

### Baremetal Disaster Recovery Test

```cucumber
Feature: Automated Baremetal Rebuild
    As an Operations Engineer
    I want to test my Disaster Recovery Plan
    So I have confidence in my Blender Render Automation SaaS (BRASS) Operational Recovery Plan
    
Scenario: Tornado wipes out Blender Render SaaS grid overnight
    Given I follow my 'Blender Render Automation SaaS (BRASS) Operational Recovery Plan'
    And I have all resources documented in 'Blender Render Automation SaaS (BRASS) Operational Recovery Plan'
    When I execute 'Blender Render Automation SaaS (BRASS) Operational Recovery Plan'
    Then I expect the 'Blender Render Automation SaaS (BRASS) Artist Project Validation Test' to pass
    
```

## Documents

1. []()
