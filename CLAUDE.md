# CLAUDE.md

This repository is a minimal WPF application scaffold intended for Figma-driven UI work. Treat the current codebase as a blank shell: there is no established design system, component library, or asset pipeline yet.

## Current Architecture

- Solution: `WPF_Playground.sln`
- App project: `src/FigmaTestApp/FigmaTestApp.csproj`
- UI framework: WPF on .NET 10 (`net10.0-windows`)
- Language/runtime: C# with nullable reference types and implicit usings enabled

### Entry Points

- `src/FigmaTestApp/App.xaml` starts `MainWindow.xaml`
- `src/FigmaTestApp/App.xaml.cs` is empty aside from the `Application` partial class
- `src/FigmaTestApp/MainWindow.xaml` currently contains only an empty `Grid`
- `src/FigmaTestApp/MainWindow.xaml.cs` only calls `InitializeComponent()`

## 1) Design System Structure

### Token Definitions

**Current state:** no design tokens are defined yet.

- `src/FigmaTestApp/App.xaml` has an empty `<Application.Resources>` block.
- There are no `ResourceDictionary` files, merged dictionaries, or theme files in the repo.
- There is no token transformation pipeline (no Style Dictionary, Figma token export, JSON token source, or MSBuild transform step).

**Implication for Figma integration:**

- Add tokens as WPF resources first, ideally in dedicated `ResourceDictionary` files.
- Use one source of truth for colors, spacing, typography, radii, shadows, and animation durations.
- If Figma tokens are introduced later, map them into WPF resources rather than scattering values across XAML.

**Recommended target structure:**

```text
src/FigmaTestApp/
  Resources/
    Theme.xaml
    Colors.xaml
    Typography.xaml
    Spacing.xaml
    Elevation.xaml
```

### Token Format

No existing format is enforced. If you add tokens, prefer WPF-friendly resource keys such as:

```xaml
<Color x:Key="Color.Primary">#FF2563EB</Color>
<SolidColorBrush x:Key="Brush.Primary" Color="{StaticResource Color.Primary}" />
<CornerRadius x:Key="Radius.Medium">8</CornerRadius>
<Thickness x:Key="Spacing.M">16</Thickness>
```

### Token Transformations

None exist today. If you later sync from Figma/MCP, document any conversion rules explicitly, for example:

- Figma color variables → `Color` / `SolidColorBrush`
- Figma text styles → `FontFamily`, `FontSize`, `FontWeight`, `LineHeight`
- Figma spacing tokens → `Thickness` / numeric constants

## 2) Component Library

### Where UI Components Live

There is currently no reusable component library, but the scaffold now includes placeholder folders for future controls and views.

- `MainWindow.xaml` is the only visible UI surface.
- No `UserControl`, `CustomControl`, `ControlTemplate`, or `DataTemplate` library exists yet.
- No storybook-like preview app or documentation site exists in the repository.

### Component Architecture

Current pattern is the default WPF window/XAML code-behind model:

```xaml
<Window x:Class="FigmaTestApp.MainWindow"
        ...
        Title="FigmaTestApp" Height="450" Width="800">
    <Grid>
    </Grid>
</Window>
```

```csharp
public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();
    }
}
```

### Rules for Future Components

- Put reusable controls in `src/FigmaTestApp/Controls/` or `src/FigmaTestApp/Components/`.
- Prefer XAML `UserControl`s for composed UI and `ControlTemplate`s for highly reusable shell-like pieces.
- Keep code-behind minimal; use it only for wiring, not layout logic.
- If a component becomes design-system-grade, move its visuals into shared resources/templates.

### Documentation / Storybook

None exists today. If you add many reusable controls, create one of the following:

- a preview window inside the app
- a `Docs/` or `Playground/` page for each component
- a separate storybook-style WPF app if the component surface grows substantially

## 3) Frameworks & Libraries

### UI Frameworks

- WPF is the only UI framework in use.
- No React, Vue, Blazor, Avalonia, WinUI, or MAUI code is present.

### Styling Frameworks

- No third-party styling library is currently used.
- There is no Material Design in XAML, Fluent theme package, or custom theme system.
- Styling is effectively default WPF styling at the moment.

### Build System / Bundler

- Build system: .NET SDK-style project (`Microsoft.NET.Sdk`)
- Output type: `WinExe`
- Target framework: `net10.0-windows`
- WPF enabled via `<UseWPF>true</UseWPF>`

Relevant project file:

- [src/FigmaTestApp/FigmaTestApp.csproj](src/FigmaTestApp/FigmaTestApp.csproj)

### Local Build / Run Conventions

The repo includes VS Code tasks and launch settings:

- build: `build FigmaTestApp Debug x64`
- build: `build FigmaTestApp Release x64`
- publish: `publish FigmaTestApp Release win-x64`

Launch config points to the generated DLL under `bin/`.

## 4) Asset Management

### Storage and Referencing

No asset pipeline exists yet.

- There are no tracked images, SVGs, videos, or icon files in the repository.
- `src/FigmaTestApp/Resources` now exists as the home for shared tokens and styles.
- `src/FigmaTestApp/Assets`, `src/FigmaTestApp/Controls`, `src/FigmaTestApp/Views`, and `src/FigmaTestApp/ViewModels` now exist as placeholders for future work.
- There is still no `Content`, `Resource`, or `EmbeddedResource` item in the `.csproj`.

### Optimization / CDN

- No optimization tooling is configured.
- No CDN configuration exists.
- No bundling, compression, or image transformation step is present.

### Rules for Adding Assets

- Store static assets in a dedicated `Assets/` or `Resources/` folder under the app project.
- Use WPF resource URIs consistently once assets are introduced.
- Prefer vector assets for icons and scalable UI chrome.
- If adding large media, define clear naming and size conventions early.

Example future pattern:

```xml
<Image Source="pack://application:,,,/FigmaTestApp;component/Assets/logo.png" />
```

## 5) Icon System

### Current State

There is no icon system yet.

- No icon files were found in the repository.
- No icon font, SVG sprite, or shared icon component is defined.
- No naming convention exists yet.

### Rules for Future Icons

- Keep icons in a single folder such as `Assets/Icons/`.
- Use a stable naming convention, for example `24/chevron-left.svg` or `ic-chevron-left.svg`.
- Centralize icon rendering through a reusable component or helper so the UI does not reference raw paths everywhere.
- If using icon fonts or glyphs later, document the mapping table in the repo.

## 6) Styling Approach

### CSS / XAML Methodology

There is no CSS; this is WPF/XAML.

- No CSS Modules, Styled Components, Tailwind, or utility CSS exists.
- Styling is currently implicit and local to each XAML file.
- `App.xaml` has no global styles yet.

### Global Styles

None exist currently beyond the merged `Resources/Theme.xaml` dictionary. The app shell now loads shared resources through `App.xaml`.

Recommended pattern when styling begins:

```xaml
<Application.Resources>
    <ResourceDictionary>
        <ResourceDictionary.MergedDictionaries>
            <ResourceDictionary Source="Resources/Colors.xaml" />
            <ResourceDictionary Source="Resources/Typography.xaml" />
            <ResourceDictionary Source="Resources/Components.xaml" />
        </ResourceDictionary.MergedDictionaries>
    </ResourceDictionary>
</Application.Resources>
```

### Responsive Design

      <ResourceDictionary Source="Resources/Theme.xaml" />

Rules:

- Avoid hard-coding widths/heights unless the design truly requires it.
- Use alignment, `Grid` star sizing, and shared spacing tokens instead of fixed pixel constants.
- Prefer `Viewbox` or vector assets for scalable iconography.

### Shared resource shell

```xaml
<ResourceDictionary>
  <ResourceDictionary.MergedDictionaries>
    <ResourceDictionary Source="Colors.xaml" />
    <ResourceDictionary Source="Typography.xaml" />
    <ResourceDictionary Source="Spacing.xaml" />
    <ResourceDictionary Source="Components.xaml" />
  </ResourceDictionary.MergedDictionaries>
</ResourceDictionary>
```

File: [src/FigmaTestApp/Resources/Theme.xaml](src/FigmaTestApp/Resources/Theme.xaml)

## 7) Project Structure

### Overall Organization

The repo is intentionally small:

```text
WPF_Playground/
  README.md
  WPF_Playground.sln
  .vscode/
  src/
    FigmaTestApp/
      App.xaml
      App.xaml.cs
      AssemblyInfo.cs
      Assets/
      Controls/
      FigmaTestApp.csproj
      MainWindow.xaml
      MainWindow.xaml.cs
      Resources/
      ViewModels/
      Views/
```

### Feature Organization Pattern

No feature-based architecture exists yet. Everything lives in one app project.

If the app grows, use one of these patterns consistently:

- by feature: `Features/Checkout`, `Features/Profile`, `Features/Settings`
- by layer: `Views/`, `ViewModels/`, `Models/`, `Services/`, `Controls/`, `Resources/`
- by design system: `Resources/`, `Controls/`, `Icons/`, `Tokens/`

### Current File Roles

- `App.xaml` — application startup and future global resources
- `MainWindow.xaml` — main shell UI
- `AssemblyInfo.cs` — WPF theme metadata
- `.vscode/tasks.json` / `.vscode/launch.json` — local build and debug helpers

## Figma Integration Rules for This Repo

When translating Figma designs into this codebase:

1. Prefer creating shared WPF resources before duplicating style values.
2. Map design tokens into `ResourceDictionary` files and reference them with `{StaticResource}`.
3. Build reusable WPF controls for repeated Figma components instead of pasting identical XAML.
4. Keep code-behind thin; move behavior into view models or services when logic grows.
5. Add assets deliberately and record where they live and how they are referenced.
6. Keep the repository’s current simplicity intact unless the design system genuinely requires more structure.

## Key Snippets

### Empty global resources

```xaml
<Application.Resources>
    
</Application.Resources>
```

File: [src/FigmaTestApp/App.xaml](src/FigmaTestApp/App.xaml)

### Minimal window shell

```xaml
<Window ... Title="FigmaTestApp" Height="450" Width="800">
    <Grid>
    </Grid>
</Window>
```

File: [src/FigmaTestApp/MainWindow.xaml](src/FigmaTestApp/MainWindow.xaml)

### WPF project configuration

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>WinExe</OutputType>
    <TargetFramework>net10.0-windows</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
    <UseWPF>true</UseWPF>
  </PropertyGroup>
</Project>
```

File: [src/FigmaTestApp/FigmaTestApp.csproj](src/FigmaTestApp/FigmaTestApp.csproj)

## Practical Default Assumptions

- Treat all current visuals as provisional scaffolding.
- Assume no design tokens exist unless you add them.
- Assume no component library exists unless the repo gains `UserControl`s or templates.
- Prefer WPF-native patterns over introducing a new framework unless the project scope expands.