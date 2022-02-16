# Pattern Matching

`switch(enumType)` in C# needs to account for "unknown" enum value added later by throwing an exception. For example —

```csharp
enum ScheduleType { Once, Daily }

switch(scheduleType) {
  case Once:
  break;

  case Daily:
  break;

  default:
    // if someone added `Weekly`, it'd be only caught here
    throw new NotSupportedException($"Unknown ScheduleType {scheduleType}");
}
```

However in F#, it would result in `warning FS0025: Incomplete pattern matches on this expression`.

```xml
<!-- this is in .fsproj project file -->
<PropertyGroup>
  <!-- other properties... -->

  <TreatWarningsAsErrors>true</TreatWarningsAsErrors>
</PropertyGroup>
```

```fsharp
type ScheduleType = Once | Daily | Weekly

// FS0025: Incomplete pattern matches on this expression
match scheduleType with
  | Once -> // do something
  | Daily -> // do another
```
