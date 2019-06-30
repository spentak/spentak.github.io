---
layout: default
title: C++ component won't show in Blueprints
parent: C++
nav_order: 1
comments: true
---
## Unreal Engine Components Not Showing In Blueprint

So this seems really simple when I write it out, but I lost two hours of my life to this. Basically, I was duplicating Blueprints on a health bar [UWidgetComponent](https://api.unrealengine.com/INT/API/Runtime/UMG/Components/UWidgetComponent/index.html) and I knew I needed to move the logic into C++ for code reduction.

I wrote the code, and even got the health bar widget working in the game.  The problem was, inside of the Blueprint Editor, my `UWidgetComponent` was nowhere to be found and I couldn't edit it.  I thought I was going crazy.

The problem was I had the widget code inside of `BeginPlay` instead of the constructor.

Don't do this
{: .label .label-red }

<div class="code-example" markdown="1">
```csharp
void AGameCharacter::BeginPlay() {
  HealthBarWidget = CreateDefaultSubobject<UWidgetComponent>(this, TEXT("HealthBarWidget"));
  HealthBarWidget->SetupAttachment(RootComponent);
}
```
</div>

Instead do this
{: .label }

<div class="code-example" markdown="1">
```csharp
AGameCharacter::AGameCharacter() {
  HealthBarWidget = CreateDefaultSubobject<UWidgetComponent>(this, TEXT("HealthBarWidget"));
  HealthBarWidget->SetupAttachment(RootComponent);
}
```
</div>
