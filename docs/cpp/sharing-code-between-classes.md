---
layout: default
title: Sharing C++ Code Between Classes
parent: C++
nav_order: 2
comments: true
---

## How To Share C++ Code Among Unrelated Unreal Engine Classes

I use Blueprint as much as I can. When I need to re-use code or need inheritance, that is when I move things to C++. It's just better than working with parent/child blueprints.

I recently ran into the issue where I had two different classes that needed shared functionality but were un-related. I had a Character object that could be damaged and attack other objects.  I also had a non-skeletal mesh actor (A tower defense tower) that also needed those exact same properties and functions.

#### What I Needed

<div class="code-example" markdown="1">

| TowerActor   | EnemyCharacter    |
|:-------------|:------------------|
| onHit()      | onHit()           |
| int32 health | int32 health      |
| onHpZero()   | onHpZero()        |
| int32 atkDmg | int32 atkDmg      |

</div>

I had about 20 properties, functions, and events I needed shared across my Actor and Character.

The problem was I couldn't modify Unreal's `ACharacter` class to point to a custom `APawn` subclass where I might implement that shared functionality. I had to choose to copy and paste the code (bugs) or to hack something together.

So what did I do? I hacked my tower and made it a skeletal mesh, which meant they could both use that code without me having to write it twice.  This had many issues, including some collision properties and other hacks I had to do to use it properly.  It was really bugging me.

Normally, in other languages I would use an Interface or Protocol to solve this problem. C++ doesn't have the concept of Interfaces.  

### Unreal UINTERFACE

Then I found out about [UINTERFACE](https://docs.unrealengine.com/en-US/Programming/UnrealArchitecture/Reference/Interfaces/index.html).  

They got me really close to a model more similar to dependency injection. But they still had the issue of not being able to support properties - ie no variables.

Local UE4 Answers contributor Rama mentioned a way of getting properties with `UINTERFACE` using [virtual getter functions](https://forums.unrealengine.com/development-discussion/c-gameplay-programming/25709-member-variable-declaration-not-allowed-in-interfaces).

This was cool, but still felt a little hackery. And I ran into some issues.  But at the end of the day I don't like using things as they weren't intended.

### Unreal Multiple Inheritance

The other option I wanted to try was multiple inheritance. I'm not a fan of it but it could have worked.  I came to find out that Unreal Engine does not support multiple inheritance.  So there was no way I could create a custom Character that inherits from `ACharacter` as well as my base actor object.

### The Solution: UActorComponent

Finally I found the *right* solution. That is to use [UActorComponent](https://api.unrealengine.com/INT/API/Runtime/Engine/Components/UActorComponent/index.html).

`UActorComponent` is a great way to achieve clean [composition](https://en.wikipedia.org/wiki/Composition_over_inheritance) in Unreal design.

From the [Unreal docs](https://docs.unrealengine.com/en-US/Programming/UnrealArchitecture/Actors/Components/index.html#actorcomponents):

```
Actor Components (class UActorComponent) are most useful for abstract behaviors such as movement,
inventory or attribute management, and other non-physical concepts. Actor Components do not
have a transform, meaning they do not have any physical location or rotation in the world.
```
### Easy Steps

- [x] Created a HealthComponent
- [x] Created a AttackComponent
- [x] Add these components to any UObject
- [x] In Blueprint, grab components and call functions or set values


Just add the components
{: .label }

<div class="code-example" markdown="1">
```csharp
AGameCharacter::AGameCharacter() {
  healthComponent = CreateDefaultSubobject<UHealthComponent>(this, TEXT("HealthComponent"));
  healthComponent->SetupAttachment(RootComponent);

  attackComponent = CreateDefaultSuboject<UAttackComponent>(this, TEXT("AttackComponent"));
  attackComponent->SetupAttachment(RootComponent);
}
```
</div>

{% include disqus.html %}
