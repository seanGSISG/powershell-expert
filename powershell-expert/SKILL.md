---
name: powershell-expert
description: Develop PowerShell scripts, tools, modules, and GUIs following Microsoft best practices. Use when writing PowerShell code, creating Windows Forms/WPF interfaces, working with PowerShell Gallery modules, or needing cmdlet/module recommendations. Covers script development, parameter design, pipeline handling, error management, and GUI creation patterns. Verifies module availability and cmdlet syntax against live documentation when accuracy is critical.
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

### 2. GUI Development
Windows Forms for simple dialogs, WPF/XAML for complex interfaces:

```powershell
Add-Type -AssemblyName System.Windows.Forms
Add-Type -AssemblyName System.Drawing

$form = New-Object System.Windows.Forms.Form -Property @{
    Text          = 'Title'
    Size          = New-Object System.Drawing.Size(400, 300)
    StartPosition = 'CenterScreen'
}
```

See [gui-development.md](references/gui-development.md) for controls, events, and templates.

### 3. PowerShell Gallery Integration
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
| **Azure** | `Az`, `Az.Compute`, `Az.Storage` |
| **Testing** | `Pester`, `PSScriptAnalyzer` |
| **Console** | `PSReadLine`, `Terminal-Icons` |
| **Secrets** | `Microsoft.PowerShell.SecretManagement` |
| **Web** | `Pode` (web server), `PoshRSJob` (async) |
| **GUI** | `WPFBot3000`, `PSGUI` |

## Live Verification

Use WebFetch and WebSearch to verify accuracy when:
- **Recommending modules** - Confirm they exist and are actively maintained
- **Citing cmdlet syntax** - Verify parameters haven't changed
- **Checking version requirements** - Confirm compatibility information

### Verify Module Exists
```
WebFetch: https://www.powershellgallery.com/packages/{ModuleName}
Prompt: "Extract module name, latest version, last updated date, total downloads, and whether it's deprecated or unlisted"
```

### Verify Cmdlet Syntax
```
WebFetch: https://learn.microsoft.com/en-us/powershell/module/{module}/{cmdlet-name}
Prompt: "Extract the cmdlet syntax, required parameters, and any version requirements"
```

### Search for Current Information
```
WebSearch: "PowerShell {topic} site:learn.microsoft.com"
WebSearch: "PSResourceGet {cmdlet} 2024" (for recent changes)
```

### When to Verify
| Scenario | Action |
|----------|--------|
| User asks "does module X exist?" | Always verify via WebFetch |
| Recommending a specific module | Verify it's not deprecated |
| Providing exact cmdlet syntax | Verify against current docs |
| Module version requirements | Check gallery for latest version |
| General best practices | Static references are sufficient |

## Documentation Resources

- **PowerShell Docs**: https://learn.microsoft.com/en-us/powershell/
- **Module Browser**: https://learn.microsoft.com/en-us/powershell/module/
- **PowerShell Gallery**: https://www.powershellgallery.com
- **GitHub Docs**: https://github.com/MicrosoftDocs/PowerShell-Docs

## References

- **[best-practices.md](references/best-practices.md)** - Naming, parameters, pipeline, error handling, code style
- **[gui-development.md](references/gui-development.md)** - Windows Forms, WPF, controls, events, templates
- **[powershellget.md](references/powershellget.md)** - Find, install, update, publish modules
