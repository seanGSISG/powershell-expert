---
name: powershell-expert
description: Develop PowerShell scripts, tools, modules, and GUIs following Microsoft best practices. Use when writing PowerShell code, creating Windows Forms/WPF interfaces, working with PowerShell Gallery modules, or needing cmdlet/module recommendations. Covers script development, parameter design, pipeline handling, error management, and logging. Verifies module availability and cmdlet syntax against live documentation when accuracy is critical.
---

# PowerShell Expert

Develop production-quality PowerShell scripts, tools, and GUIs using Microsoft best practices and the PowerShell ecosystem.

## Quick Reference

### Script Structure
```powershell
#Requires -Version 5.1

<#
.SYNOPSIS
    Brief description.
.DESCRIPTION
    Detailed description.
.PARAMETER Name
    Parameter description.
.EXAMPLE
    Example-Usage -Name 'Value'
#>

[CmdletBinding()]
param(
    [Parameter(Mandatory, ValueFromPipeline)]
    [ValidateNotNullOrEmpty()]
    [string[]]$Name,

    [switch]$Force
)

begin {
    # One-time setup
}

process {
    foreach ($item in $Name) {
        # Per-item processing
    }
}

end {
    # Cleanup
}
```

### Function Template
```powershell
function Verb-Noun {
    [CmdletBinding(SupportsShouldProcess)]
    param(
        [Parameter(Mandatory, Position = 0)]
        [string]$Name,

        [Parameter(ValueFromPipelineByPropertyName)]
        [Alias('CN')]
        [string]$ComputerName = $env:COMPUTERNAME,

        [switch]$PassThru
    )

    process {
        if ($PSCmdlet.ShouldProcess($Name, 'Action')) {
            # Implementation
            if ($PassThru) { Write-Output $result }
        }
    }
}
```

## Workflow

### 1. Script Development
Follow naming and parameter conventions:
- **Verb-Noun** format with approved verbs (`Get-Verb`)
- **Strong typing** with validation attributes
- **Pipeline support** via `ValueFromPipeline`
- **-WhatIf/-Confirm** for destructive operations

See [best-practices.md](references/best-practices.md) for complete guidelines.

### 2. PowerShell Gallery Integration
Search and install modules using PSResourceGet:

```powershell
# Search gallery
Find-PSResource -Name 'ModuleName' -Repository PSGallery

# Install module
Install-PSResource -Name 'ModuleName' -Scope CurrentUser -TrustRepository
```

Use [scripts/Search-Gallery.ps1](scripts/Search-Gallery.ps1) for enhanced search.

See [powershellget.md](references/powershellget.md) for full cmdlet reference.

## Key Patterns

### Error Handling
```powershell
try {
    $result = Get-Content -Path $Path -ErrorAction Stop
}
catch [System.IO.FileNotFoundException] {
    Write-Error "File not found: $Path"
    return
}
catch {
    throw
}
```

### Splatting for Readability
```powershell
$params = @{
    Path        = $sourcePath
    Destination = $destPath
    Recurse     = $true
    Force       = $true
}
Copy-Item @params
```

### Pipeline Best Practices
```powershell
# Stream output immediately
foreach ($item in $collection) {
    Process-Item $item | Write-Output
}

# Accept pipeline input
param(
    [Parameter(ValueFromPipeline)]
    [string[]]$InputObject
)
process {
    foreach ($obj in $InputObject) {
        # Process each
    }
}
```

## Module Recommendations

When recommending modules, search the PowerShell Gallery:

| Category | Popular Modules |
|----------|----------------|
| **Azure** | `Az`, `Az.Compute`, `Az.Storage`, `Az.Accounts` |
| **Entra** | `Microsoft.Graph`, `ExchangeOnlineManagement`, `Microsoft.Entra` |
| **Testing** | `Pester`, `PSScriptAnalyzer` |
| **Console** | `PSReadLine`, `Terminal-Icons` |
| **Secrets** | `Az.KeyVault`, `Microsoft.PowerShell.SecretManagement` |
| **Web** | `Pode` (web server), `PoshRSJob` (async) |


## Live Verification

You MUST verify information against live sources when accuracy is critical. Do not rely solely on training data for module availability or cmdlet syntax.

**Tools to use:**
- **microsoft-docs Plugin and microsoft-learn MCP**: Retrieve and parse specific documentation with your builtin microsoft-docs skill and microsoft-learn mcp tool
- **context7**: Utilize Context7 mcp tool for further documentation context

### When Verification is Required

| Scenario | Action |
|----------|--------|
| User asks "does module X exist?" | **MUST** verify via PowerShell Gallery |
| Recommending a specific module | **MUST** verify it exists and isn't deprecated |
| Providing exact cmdlet syntax | **SHOULD** verify against Microsoft Docs |
| Module version requirements | **MUST** check gallery for current version |
| General best practices | Static references are sufficient |

### Step 1: Verify Module on PowerShell Gallery

When recommending or checking a module, **use the WebFetch tool** to verify it exists:

**WebFetch call:**
- **URL**: `https://www.powershellgallery.com/packages/{ModuleName}`
- **Prompt**: `Extract: module name, latest version, last updated date, total downloads, and whether it shows any deprecation warning or 'unlisted' status`

**If WebFetch returns 404 or error**: The module likely doesn't exist. **Use the microsoft-learn mcp** to confirm

### Step 2: Verify Cmdlet Syntax (When Needed)

- Use microsoft-docs, microsoft-learn, context7 when needed

### Step 3: Fallback Strategies

If the WebFetch or WebSearch or other tools are unavailable or return errors:

1. **For module verification**: Execute `Search-Gallery.ps1` from this skill:
   ```powershell
   ~/.claude/skills/powershell-expert/scripts/Search-Gallery.ps1 -Name 'ModuleName'
   ```

2. **For cmdlet syntax**:
   ```powershell
   Get-Help Cmdlet-Name -Full
   Get-Command Cmdlet-Name -Syntax
   ```

### Verification Examples

**Good** (verified with live data):
> "The ImportExcel module (v7.8.10, updated Oct 2024, 17M+ downloads)
> provides Export-Excel for creating spreadsheets without Excel installed."

**Bad** (unverified claim):
> "Use the Excel-Tools module to export data." ← May not exist!

## Documentation Resources

- **PowerShell Docs**: https://learn.microsoft.com/en-us/powershell/
- **Module Browser**: https://learn.microsoft.com/en-us/powershell/module/
- **PowerShell Gallery**: https://www.powershellgallery.com
- **GitHub Docs**: https://github.com/MicrosoftDocs/PowerShell-Docs

## References

- **[best-practices.md](references/best-practices.md)** - Naming, parameters, pipeline, error handling, code style
- **[gui-development.md](references/gui-development.md)** - Windows Forms, WPF, controls, events, templates
- **[powershellget.md](references/powershellget.md)** - Find, install, update, publish modules
