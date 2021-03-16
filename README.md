# Space-Engineers-Battery-Status-Script
A simple script to display battery usage and charge in an ingame lcd display

```c#
List<IMyTerminalBlock> batteryTerminalBlocks = new List<IMyTerminalBlock>();
IMyTextPanel lcd;

public Program()
{
    Runtime.UpdateFrequency = UpdateFrequency.Update1;

    IMyBlockGroup batteryGroup = GridTerminalSystem.GetBlockGroupWithName("Base Batteries");
    batteryGroup.GetBlocks(batteryTerminalBlocks);

    lcd = GridTerminalSystem.GetBlockWithName("Power Status LCD") as IMyTextPanel;
    //light = GridTerminalSystem.GetBlockWithName("light") as IMyInteriorLight;
}

public void Save()
{
    // Called when the program needs to save its state. Use
    // this method to save your state to the Storage field
    // or some other means. 
    // 
    // This method is optional and can be removed if not
    // needed.
}

private string makePowerUsageStatus(List<IMyBatteryBlock> batteries)
{
    return "Power usage: " + batteries.Sum(battery => battery.CurrentOutput).ToString("0.00") + " / " + batteries.Sum(battery => battery.MaxOutput).ToString("0.00");
}

private string makeMaxPowerCapacityStatus(List<IMyBatteryBlock> batteries)
{
    return "Stored Power: " + batteries.Sum(battery => battery.CurrentStoredPower).ToString("0.00") + " / " + batteries.Sum(battery => battery.MaxStoredPower).ToString("0.00");
}

private float calcPowerUsagePercentage(List<IMyBatteryBlock> batteries)
{
    return (batteries.Sum(battery => battery.CurrentOutput) / batteries.Sum(battery => battery.MaxOutput)) * 100;
}

private void displayOnLcd(List<string> lines)
{
    lcd.WriteText(string.Join("\n", lines), false);
}

public void Main(string argument, UpdateType updateSource)
{
    List<IMyBatteryBlock> batteries = batteryTerminalBlocks.Cast<IMyBatteryBlock>().ToList();

    List<string> statusText = new List<string>();

    statusText.Add(makeMaxPowerCapacityStatus(batteries));
    statusText.Add(makePowerUsageStatus(batteries));

    displayOnLcd(statusText);
}
```
