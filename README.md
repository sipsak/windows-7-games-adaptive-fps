# Windows 7 Games Adaptive FPS

**High refresh rate patch for classic Windows 7 games on Windows 10 and Windows 11.**

Bring classic Solitaire, Spider Solitaire, FreeCell, Hearts, Minesweeper, Mahjong Titans, and Purble Place to modern high refresh rate displays. This patch replaces the original engine's fixed update timing with adaptive timing based on your monitor's detected refresh rate, while preserving original animation timing.

Designed for the x64 Windows 7 Games for Windows 11 / Windows 10 installation. This is a patch for an existing game installation, not a replacement game collection.

## Features

- Automatic refresh rate detection at game startup and when the Direct3D rendering device is recreated
- Adaptive frame timing for 60 Hz, 120 Hz, 144 Hz, 165 Hz, 240 Hz, 360 Hz, and other supported refresh rates
- Original animation durations preserved
- Corrected fireworks and other legacy particle effects by preserving their original 30 Hz physics timing
- Fix for the yellow selection-glow artifact when moving multiple cards
- SHA-256 verification of supported executable versions and patched output
- Original executable backups and reversible patching
- Support for partial installations containing only some of the classic games
- Standalone PowerShell script; no additional runtime, game launcher, background service, or driver required

## Supported games

| Game | Support |
|:--|:--:|
| Solitaire | Adaptive FPS |
| Spider Solitaire | Adaptive FPS + selection-glow fix |
| FreeCell | Adaptive FPS |
| Hearts | Adaptive FPS |
| Minesweeper | Adaptive FPS |
| Mahjong Titans | Adaptive FPS |
| Purble Place | Adaptive FPS |
| Chess Titans | Not required (native refresh-rate support) |

The patch targets the known Windows 7 RTM x64 executables with file version `6.1.7600.16385` (`win7_rtm.090713-1255`). Exact SHA-256 fingerprints are checked before modification. Other executable versions, including some files with the same version string, may not be compatible.

**No Microsoft game executables, DLLs, graphics, sounds, or other game assets are distributed in this repository.** You need an existing compatible installation.

## Installation

### Requirements

- Windows 10 or Windows 11 with a compatible x64 classic Windows 7 games installation
- Windows PowerShell 5.1 or PowerShell 7
- Permission to write to the game installation directory (typically administrator access)

### Apply the patch

1. Download [Patch-ClassicGames.ps1](Patch-ClassicGames.ps1) using the file's Download button or download this repository as a ZIP.
2. Close all classic games.
3. Open PowerShell as Administrator and navigate to the folder containing the script.
4. Check compatibility:

```powershell
& ([scriptblock]::Create([IO.File]::ReadAllText((Join-Path $PWD 'Patch-ClassicGames.ps1')))) -Check
```

5. Apply the patch:

```powershell
& ([scriptblock]::Create([IO.File]::ReadAllText((Join-Path $PWD 'Patch-ClassicGames.ps1'))))
```

The default installation directory is `C:\Program Files\Microsoft Games`. For a custom installation directory, pass the `-GameRoot` parameter:

```powershell
& ([scriptblock]::Create([IO.File]::ReadAllText((Join-Path $PWD 'Patch-ClassicGames.ps1')))) -GameRoot 'D:\Microsoft Games'
```

The script verifies supported files before making changes and backs up the original executables to the `FPS_Original_Backup` directory inside your game installation. Missing games are skipped. Existing game shortcuts continue to work. If v1.0.0 is already installed, run the latest script again to update without reinstalling the games.

## Restore the original games

Close the games and run the script with the `-Restore` parameter:

```powershell
& ([scriptblock]::Create([IO.File]::ReadAllText((Join-Path $PWD 'Patch-ClassicGames.ps1')))) -Restore
```

Use the same `-GameRoot` parameter if you chose a custom installation directory. Keep the original backups so the patch can be reversed.

## How adaptive FPS works

| Monitor refresh rate | Target update rate | Target frame interval |
|---:|---:|---:|
| 60 Hz | 60 FPS | 16.67 ms |
| 120 Hz | 120 FPS | 8.33 ms |
| 144 Hz | 144 FPS | 6.94 ms |
| 165 Hz | 165 FPS | 6.06 ms |
| 240 Hz | 240 FPS | 4.17 ms |
| 360 Hz | 360 FPS | 2.78 ms |

Instead of simply speeding up a fixed animation loop, the patch adjusts both the high-resolution game timing and animation time steps. It reads the Windows display refresh rate during initialization and uses the selected Direct3D adapter's display mode when available. The original fixed-rate sprite animation durations are preserved. Legacy particle effects (including victory fireworks) retain their original 30 Hz physics updates, independently of the adaptive game timing, so their speed and trajectory stay consistent across refresh rates. If a valid refresh rate cannot be determined, the target falls back to 60 Hz.

The refresh rate determines the **target update rate**, not a guarantee of measured rendering FPS. Actual frame presentation depends on graphics hardware, drivers, Windows display composition, and vertical synchronization.

If you change the monitor or its refresh rate while a game is open, restart the game to ensure the timing is refreshed.

## Testing

- All seven patched games launched and closed successfully on Windows 11 with a real 120 Hz display. Their internal target rate was verified to be 120 Hz, with a frame interval of approximately 8.33 ms.
- Dynamic timing was verified in test copies for simulated 60 Hz, 144 Hz, 240 Hz, and 360 Hz values. These tests were not physical monitor FPS benchmarks.
- The selection-glow fix was verified during gameplay.
- Clean original-to-patched reconstruction, SHA-256 verification, and restoration were tested for all seven games.
- The v1.0.1 legacy particle timing correction was validated against the original 30 Hz update cadence.

## Troubleshooting

- **Unsupported executable version:** Use `-Check` to identify incompatible files. The patch deliberately refuses to modify unknown binaries.
- **Access denied:** Close the games and run PowerShell as Administrator.
- **Missing games:** The patch skips games that are not installed in the selected directory.
- **Refresh rate changed:** Restart the game.
- **Reporting a problem:** Open a GitHub issue with your game name, Windows version, refresh rate, script output, and the executable's SHA-256 hash. Please do not upload original game binaries or assets.

## License and disclaimer

This is an independent, unofficial compatibility and performance patch. It is not affiliated with or endorsed by Microsoft. Windows, the original games, and their assets belong to their respective rights holders. Obtain and use the original software lawfully.

The MIT license applies to this patching project only, not to the original games.
