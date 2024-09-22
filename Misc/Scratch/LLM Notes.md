vramlet cope survival logs
## My System
```
O/S: Windows 10 IoT Enterprise LTSC 64-bit
CPU: 11th Gen Intel Core i9-11900K @ 3.50GHz; Cores 8 / Threads 16
RAM: 128GB
GPU: RTX 3070 Ti, 8192MB
M.2: 953GB (loading most models off a NAS)
S/W: koboldcpp + SillyTavern
```

## koboldcpp settings
```bat
koboldcpp_cu12.exe
--model "S:\roms\ai\models\text\Mistral-Nemo-Instruct-2407-IQ4_XS.gguf"
--contextsize 12288 --threads 7 
--highpriority --foreground --skiplauncher --onready "SillyTavern.bat"
--nommap --usemlock
--usecublas normal 0 0
--blasthreads 14
--blasbatchsize 2048
--gpulayers -1
```

- having `--usecublas normal 0 1` made things really strange.
	- disabling it was the difference between my VRAM hitting 8GB (physical cap)
	- vs it hitting 16GB (Process Explorer reports I have more VRAM from somewhere)
- `mlock` because lots of system RAM (afaik it helps if I can spare any)
- `mmap` because loading models via NAS, want to reduce network / remote disk io

## dwm.exe
- You can always kill dwm.exe for a brief interruption.
- disable fast startup
	- Reminder to set windows to not disable your USBs just because it is bored.
```cmd
control /name Microsoft.PowerOptions /page pageGlobalSettings
```

- Reduce the use of the hibernation file:
	- `powercfg /h /type reduced`
- Disable all the flashy UI stuff:
	- `win+R`, `SystemPropertiesPerformance`
	- choose **Adjust for best performance**
	- Then re-enable **Smooth edges of screen fonts**
- Disable HDR streaming
	- `win+alt+B` will disable HDR, from the gamebar, even if you don't have an option for it
	- `win+R`, `ms-settings:display`
	- Normally appears under: **Settings**  > **System**  > **Display** | **Windows HD Color** | **Use HDR** only if your monitors support it.
- Disabling desktop notifications decreases DWM weight?
	- `win+R`, `ms-settings:notifications`
	- Uncheck everything, flip the toggle, whatever you want.
- Disabling hardware-accelerated GPU Scheduling
	- `win+R`, `ms-settings:display-advancedgraphics`
	- Disable GPU scheduling and see if it helps, might mess stuff up probably.
- some edit a youtube video will tell you to change across 4 minutes but who knows if it will do anything
```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\Dwm]
"ForceEffectMode"=dword:00000002
```
- NVIDIA Control Panel: [disable system memory fallback](https://nvidia.custhelp.com/app/answers/detail/a_id/5490/~/system-memory-fallback-for-stable-diffusion)
	- **CUDA - Sysmem Fallback Policy**, set to be **Prefer No Sysmem Fallback**
	- Prevents memory that thinks it is fast actually being slow memory and just getting worse instead of dying.

# My Settings w/ Only Layer Differences

I tested the lower tiers of settings and misc configurations but my barometer was to be able to run stable diffusion along-side this or not have Virtual Audio Cables get twisted up by CPU stress.
## 0 layers
```
Processing Prompt [BLAS] (10553 / 10553 tokens)
Generating (31 / 250 tokens) (EOS token triggered! ID:2)
CtxLimit:10584/12288, Amt:31/250, Init:0.05s, Process:14.65s (1.4ms/T = 720.49T/s), Generate:8.12s (261.8ms/T = 3.82T/s), Total:22.76s (1.36T/s)
```

## 7 layers
```
Processing Prompt [BLAS] (10518 / 10518 tokens)
Generating (82 / 250 tokens) (EOS token triggered! ID:2)
CtxLimit:10600/12288, Amt:82/250, Init:0.05s, Process:16.33s (1.6ms/T = 644.17T/s), Generate:22.54s (274.9ms/T = 3.64T/s), Total:38.87s (2.11T/s)
```