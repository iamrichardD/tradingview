# Agent Creation Standards - META-AGENT-SOP Enhancement

**Document Date**: August 10, 2025  
**Last Updated**: August 10, 2025

## Critical Requirements for All Agent Creation

### **Date Documentation Standards**

**MANDATORY**: All agents MUST use the `date` command when documenting creation or modification dates. Never assume or hardcode dates.

#### **Required Process**:
1. **Before Creating Agent**: Execute `date` command to get current date
2. **Documentation**: Use actual date from `date` command output
3. **Agent Tools**: Ensure agent has `Bash` tool for date access
4. **Verification**: Always validate dates match actual system date

#### **Example Implementation**:
```bash
# Get current date for agent creation
date
# Output: Sun Aug 10 10:26:05 AM PDT 2025
# Use: 2025-08-10 in YAML frontmatter
```

### **Tool Requirements for Date-Dependent Agents**

**All agents that need to document dates MUST include**:
- `Bash` tool (for `date` command access)
- Standard tools based on agent type per META-AGENT-SOP guidelines

#### **Updated Tool Matrix**:
- **Strategic Agents**: `[Read, Write, Glob, Grep, TodoWrite, Bash]`
- **Development Agents**: `[Read, Write, Edit, MultiEdit, Bash, Grep, Glob, WebFetch]`
- **Documentation Agents**: `[Read, Grep, Glob, WebFetch, Bash]`
- **Temporal/Analysis Agents**: `[Read, Grep, Glob, Write, Edit, WebFetch, Bash]`

### **Agent Creation Date Standards**

#### **YAML Frontmatter Requirements**:
```yaml
created: 2025-08-10        # YYYY-MM-DD format using `date` command
modified: 2025-08-10       # YYYY-MM-DD format using `date` command
contributors: rdelgado     # From `git config user.name`
```

#### **Date Verification Process**:
1. Execute `date` command before agent creation
2. Extract date in YYYY-MM-DD format  
3. Use extracted date in agent YAML frontmatter
4. Never assume or hardcode dates
5. Verify date matches system date

### **Documentation Date Standards**

#### **All Project Documentation MUST**:
- Use `date` command to get current date
- Include date in document headers
- Update "Last Updated" fields when modifying documents
- Use consistent date format: YYYY-MM-DD

#### **Example Documentation Header**:
```markdown
# Document Title

**Creation Date**: August 10, 2025 (from `date` command)
**Last Updated**: August 10, 2025 (from `date` command)
```

### **Quality Assurance Checklist**

#### **Before Agent Creation**:
- [ ] Execute `date` command to get current date
- [ ] Verify agent tools include `Bash` for date access
- [ ] Prepare YAML frontmatter with correct date
- [ ] Validate date format (YYYY-MM-DD)

#### **After Agent Creation**:
- [ ] Verify created/modified dates match system date
- [ ] Test agent can access `date` command if needed
- [ ] Update project documentation with correct dates
- [ ] Validate all dates are consistent across files

### **Date Command Usage Examples**

#### **Getting Current Date**:
```bash
date                    # Full date/time output
date +"%Y-%m-%d"       # YYYY-MM-DD format
date +"%B %d, %Y"      # August 10, 2025 format
```

#### **In Agent Creation Context**:
```bash
# Step 1: Get date
date +"%Y-%m-%d"
# Output: 2025-08-10

# Step 2: Use in YAML
created: 2025-08-10
modified: 2025-08-10
```

### **Common Date Errors to Avoid**

#### **❌ NEVER DO**:
- Hardcode dates without verification
- Assume dates from previous sessions
- Use incorrect date formats
- Forget to include `Bash` tool for date access
- Mix date formats within same document

#### **✅ ALWAYS DO**:
- Execute `date` command before documenting
- Use consistent YYYY-MM-DD format in YAML
- Include `Bash` tool in agent specifications
- Verify dates match system date
- Update modification dates when editing

### **Integration with META-AGENT-SOP**

#### **Enhanced Step 6 (Metadata Generation)**:
1. **Execute `date` command** to get current system date
2. **Extract date** in YYYY-MM-DD format for YAML frontmatter
3. **Verify consistency** with system date before saving
4. **Update documentation** with correct dates throughout
5. **Include `Bash` tool** if agent needs date access capabilities

This enhancement ensures all agents and documentation maintain accurate, verifiable creation and modification dates using the system's actual date rather than assumptions or hardcoded values.

---

**Document Standards**: ✅ ENFORCED  
**Date Verification**: ✅ MANDATORY  
**Tool Requirements**: ✅ UPDATED  
**Quality Gates**: ✅ ENHANCED