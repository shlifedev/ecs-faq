한국어 | [English](README.en.md)

# 엔티티 컴포넌트 시스템 FAQ

이 FAQ는 ECS(Entity Component System)와 현대적인 고성능 게임 개발에 관심 있는 모든 분을 위한 문서입니다. 답변은 짧고 정확하게 작성하는 것을 목표로 하며, 모든 내용을 빠짐없이 다루지는 않습니다. 더 자세한 내용은 [참고 자료](#resources)를 확인하세요.

이 문서는 [Sander Mertens의 ECS FAQ](https://github.com/SanderMertens/ecs-faq)를 한국어로 번역한 것입니다. 아래 저자 소개와 본문의 1인칭 표현은 원저자를 가리킵니다. [영어 원문](README.en.md)도 함께 제공합니다.

FAQ에 빠진 내용이나 잘못된 내용이 있다면 이슈나 PR을 자유롭게 등록해 주세요!

## 저자 소개

저는 C와 C++용 엔티티 컴포넌트 시스템인 [Flecs](https://github.com/SanderMertens/flecs)의 개발자입니다. ECS 기능을 더 잘 구현할 방법을 늘 실험하며, 기회가 되면 그 내용을 글로 정리합니다. ECS에 관해 이야기하고 싶다면 [Discord에 참여하세요](https://discord.gg/BEzP5Rgrrp)!

## 일반 질문

- [ECS란 무엇인가요?](#what-is-ecs)
- [어떤 조건을 갖춰야 ECS라고 할 수 있나요?](#when-is-something-an-ecs)
- [ECS를 사용하는 이유는 무엇인가요?](#why-is-ecs-used)
- [어디에서 ECS를 사용하고 있나요?](#who-is-using-ecs)
- [ECS와 객체 지향 프로그래밍은 어떻게 다른가요?](#how-is-ecs-different-from-oop)
- [ECS와 엔티티-컴포넌트 프레임워크는 어떻게 다른가요?](#how-is-ecs-different-from-entity-component-frameworks)
- [ECS는 배우기 어려운가요?](#is-ecs-hard-to-learn)
- [ECS는 추상화 수준이 더 낮은가요?](#is-ecs-a-lower-level-of-abstraction)
- [ECS를 사용하면 코드를 더 많이 작성해야 하나요?](#does-ecs-require-writing-more-code)
- [ECS는 저수준 코드에 적합한가요?](#is-ecs-good-for-low-level-code)
- [어떤 언어로든 ECS를 구현할 수 있나요?](#can-ecs-be-implemented-in-any-language)
- [ECS를 직접 만들어야 할까요?](#should-i-write-my-own-ecs)
- [ECS는 빠른가요?](#is-ecs-fast)
- [ECS 코드는 재사용하기 더 쉬운가요?](#is-ecs-code-more-reusable)
- [ECS는 멀티스레딩에 적합한가요?](#is-ecs-good-for-multithreading)
- [게임 이외의 분야에서도 ECS를 사용할 수 있나요?](#can-ecs-be-used-outside-of-gaming)
- [ECS를 어떻게 시작하면 좋을까요?](#how-do-i-start-with-ecs)
- [ECS에 맞게 설계하려면 어떻게 해야 하나요?](#how-do-i-design-for-ecs)
- [ECS를 구현하는 방식에는 어떤 것들이 있나요?](#what-are-the-different-ways-to-implement-an-ecs)
- [컴포넌트는 어떻게 수정하나요?](#how-are-components-modified)
- [엔티티와 시스템은 어떻게 연결되나요?](#how-are-entities-matched-with-systems)
- [엔티티 관계란 무엇인가요?](#what-are-entity-relationships)

## 활용 방법

- [ECS에서 계층 구조는 어떻게 만드나요?](#how-to-create-a-hierarchy-in-ecs)
- [ECS에서 공간 데이터는 어떻게 저장하나요?](#how-to-store-spatial-data-in-ecs)

## 데이터 지향 설계 질문

- [데이터 지향 설계란 무엇인가요?](#what-is-data-oriented-design)
- [ECS와 데이터 지향 설계는 같은 것인가요?](#is-ecs-the-same-as-dod)
- [RAM이란 무엇인가요?](#what-is-random-access-memory)
- [CPU 캐시란 무엇인가요?](#what-is-a-cpu-cache)
- [캐시 라인이란 무엇인가요?](#what-is-a-cache-line)
- [참조 지역성이란 무엇인가요?](#what-is-locality-of-reference)
- [SIMD란 무엇인가요?](#what-is-simd)
- [벡터화란 무엇인가요?](#what-is-vectorization)
- [거짓 공유란 무엇인가요?](#what-is-false-sharing)
- [AoS란 무엇인가요?](#what-is-aos)
- [SoA란 무엇인가요?](#what-is-soa)
- [분기 예측이란 무엇인가요?](#what-is-branch-prediction)

## 용어집

- [엔티티(Entity)](#entity)
- [컴포넌트(Component)](#component)
- [태그(Tag)](#tag)
- [시스템(System)](#system)
- [쿼리(Query)](#query)
- [월드(World)](#world)
- [레지스트리(Registry)](#registry)
- [테이블(Table)](#table)
- [아키타입(Archetype)](#archetype)
- [희소 집합(Sparse set)](#sparse-set)

## ECS 프레임워크

아래 목록에는 오픈 소스와 비공개 소스 ECS 구현체, ECS 패턴을 도입한 엔진이 포함되어 있습니다. 원문에서는 지난 1년 동안 소스 코드에 의미 있는 변경이 없었던 프로젝트를 이 목록에서 제외합니다.

- [AFRAME](https://aframe.io) (HTML5/JS, MIT)
- [Arch](https://github.com/genaray/Arch) (C#, Apache)
- [Ark](https://github.com/mlange-42/ark) (Go, Apache/MIT)
- [Bang](https://github.com/isadorasophia/bang) (C#, MIT)
- [Bevy ECS](https://github.com/bevyengine/bevy) (Rust, MIT)
- [Ecsact](https://ecsact.dev) (MIT)
- [EnTT](https://github.com/skypjack/entt) (C++20, MIT)
- [Esper](https://github.com/benmoran56/esper) (Python, MIT)
- [StaticEcs](https://github.com/Felid-Force-Studios/StaticEcs) (C#, MIT)
- [fennecs](https://fennecs.tech) (C#, MIT)
- [Fireblade](https://github.com/fireblade-engine/ecs) (Swift, MIT)
- [Flecs](https://github.com/SanderMertens/flecs) (C/C++17, [C#](https://github.com/flecs-hub/flecs-cs), [Rust](https://github.com/jazzay/flecs-rs), [Zig](https://github.com/prime31/zig-flecs), [Lua](https://github.com/flecs-hub/flecs-lua), MIT)
- [Fleks](https://github.com/Quillraven/Fleks) (Kotlin Multiplatform, MIT)
- [Friflo ECS](https://github.com/friflo/Friflo.Engine.ECS) (C#, MIT)
- [Gaia ECS](https://github.com/richardbiely/gaia-ecs) (C++, MIT)
- [Hecs](https://github.com/Ralith/hecs) (Rust, Apache/MIT)
- [Photon Quantum](https://doc.photonengine.com/en-us/quantum/current/getting-started/quantum-intro) (C#, 상용 라이선스)
- [Apple RealityKit](https://developer.apple.com/documentation/realitykit) (Swift, 상용 라이선스)
- [Shipyard](https://github.com/leudz/shipyard) (Rust, Apache/MIT)
- [Unity DOTS](https://unity.com/dots) (C#, 상용 라이선스)
- [Unreal Mass](https://docs.unrealengine.com/5.0/en-US/overview-of-mass-entity-in-unreal-engine/) (C++, 상용 라이선스)
- [Zig ECS](https://github.com/prime31/zig-ecs) (Zig, MIT)
- [Morpeh](https://github.com/scellecs/morpeh) (C#, MIT)
- [Jecs](https://github.com/Ukendio/jecs) (Luau, MIT)
- [DragonECS](https://github.com/DCFApixels/DragonECS) (C#, MIT)

### 활동이 중단된 프로젝트

- [Artemis](https://github.com/junkdog/artemis-odb) (Java, 자체 라이선스)
- [DefaultEcs](https://github.com/Doraku/DefaultEcs) (C#, MIT-0)
- [Dominion](https://github.com/dominion-dev/dominion-ecs-java) (Java, MIT)
- [Entitas-Redux](https://github.com/jeffcampbellmakesgames/Entitas-Redux) (C#, MIT)
- [Entitas](https://github.com/sschmid/Entitas-CSharp) (C#, MIT)
- [EntityX](https://github.com/alecthomas/entityx) (C++11, MIT)
- [LeoEcsLite](https://github.com/Leopotam/ecslite) (C#, MIT)
- [Legion](https://github.com/amethyst/legion) (Rust, MIT)
- [luaecs](https://github.com/cloudwu/luaecs) (C/Lua, MIT)
- [tiny-ecs](https://github.com/bakpakin/tiny-ecs) (Lua, MIT)
- [LeoECS](https://github.com/Leopotam/ecs) (C#, MIT)
- [Specs](https://github.com/amethyst/specs) (Rust, Apache/MIT)
- [Svelto](https://github.com/sebas77/Svelto.ECS) (C#, MIT)
- [ME.ECS](https://github.com/chromealex/ecs) (C#, MIT)

<a id="resources"></a>

## 참고 자료

- [엔티티-컴포넌트-시스템 아키텍처를 진지하게 다루기](https://www.youtube.com/watch?v=VpiprNBEZsk) (@alice-i-cecile, YouTube)
- [엔티티-컴포넌트-시스템 패턴에서 동시성의 이론과 실제 살펴보기](https://www.youtube.com/watch?v=z2vqiVtmsuY) (Patrick Redmond, Jonathan Castello, José Manuel Calderón Trilla, Lindsey Kuper, YouTube)
- [오버워치의 게임플레이 아키텍처와 넷코드](https://www.youtube.com/watch?v=W3aieHjyNvw) (Blizzard, GDC)
- [데이터 지향 설계와 C++](https://www.youtube.com/watch?v=rX0ItVEVjHc) (Mike Acton, CppCon)
- [실용적인 최적화](https://www.youtube.com/watch?v=NAVbI1HIzCE) (Jason Booth, YouTube)
- [게임 개발에 Rust 활용하기](https://www.youtube.com/watch?v=aKLntZcp27M) (Catherine West, RustConf)
- [CPU 캐시와 캐시를 알아야 하는 이유](https://vimeo.com/97337258) (Scott Meyers, NDC)
- [느린 ECS 위에 빠른 ECS 만들기](https://youtu.be/71RSWVyOMEY) (UnitOfTime, YouTube)
- [전장의 컬링: 데이터 지향 설계의 실제 적용](https://www.gdcvault.com/play/1014491/Culling-the-Battlefield-Data-Oriented) (DICE, GDC)
- [시티즈: 스카이라인 2의 시스템을 살펴보는 인터랙티브 앱](https://captain-of-coit.github.io/cs2-ecs-explorer/#) (Captain-Of-Coit)
- [게임 엔진의 엔티티/오브젝트 시스템](https://www.youtube.com/watch?v=jjEsB611kxs) (Bobby Anguelov, 발표)
- [엔티티 컴포넌트 시스템을 위한 데이터 지향 설계 이해하기](https://www.youtube.com/watch?v=0_Byw9UMn9g) (Unity, GDC)
- [데이터 지향 설계 이해하기](https://learn.unity.com/tutorial/part-1-understand-data-oriented-design?courseId=60132919edbc2a56f9d439c3&signup=true&uv=2020.1) (Unity, 튜토리얼)
- [데이터 지향 설계](https://www.dataorienteddesign.com/dodbook/dodmain.html) (Richard Fabian, 책)
- [엔티티, 컴포넌트와 시스템](https://medium.com/ingeniouslysimple/entities-components-and-systems-89c31464240d) (Mark Jordan, 블로그)
- [Awesome Entity Component System](https://github.com/jslee02/awesome-entity-component-system) (Jeongseok Lee, 링크 모음)
- [기본적인 ECS만으로는 충분하지 않은 이유](https://ajmmertens.medium.com/why-vanilla-ecs-is-not-enough-d7ed4e3bebe5) (Sander Mertens, 블로그)
- [ECS와 Entitas의 기반 개념 정식화](https://medium.com/@icex33/formalisation-of-concepts-behind-ecs-and-entitas-8efe535d9516) (Maxim Zaks, 블로그)
- [엔티티 컴포넌트 시스템과 렌더링](https://ourmachinery.com/post/ecs-and-rendering/) (Our Machinery, 블로그)
- [Specs와 Legion: ECS를 구현하는 두 가지 매우 다른 방식](https://csherratt.github.io/blog/posts/specs-and-legion/) (Cora Sherrat, 블로그)
- [내 엔티티와 컴포넌트는 어디에 있을까](https://ajmmertens.medium.com/building-an-ecs-1-where-are-my-entities-and-components-63d07c7da742) (Sander Mertens, 블로그)
- [아키타입과 벡터화](https://medium.com/@ajmmertens/building-an-ecs-2-archetypes-and-vectorization-fe21690805f9) (Sander Mertens, 블로그)
- [엔티티 관계로 게임 만들기](https://ajmmertens.medium.com/building-games-in-ecs-with-entity-relationships-657275ba2c6c) (Sander Mertens, 블로그)
- [게임을 데이터베이스로 생각하기 시작해야 할 때인 이유](https://ajmmertens.medium.com/why-it-is-time-to-start-thinking-of-games-as-databases-e7971da33ac3) (Sander Mertens, 블로그)
- [엔티티 관계 로드맵](https://ajmmertens.medium.com/a-roadmap-to-entity-relationships-5b1d11ebb4eb) (Sander Mertens, 블로그)
- [엔티티 식별자를 최대한 활용하기](https://ajmmertens.medium.com/doing-a-lot-with-a-little-ecs-identifiers-25a72bd2647) (Sander Mertens, 블로그)
- [ECS에 상태 머신을 저장하는 것이 좋지 않은 이유](https://ajmmertens.medium.com/why-storing-state-machines-in-ecs-is-a-bad-idea-742de7a18e59) (Sander Mertens, 블로그)
- [ECS 이모저모](https://skypjack.github.io/2019-02-14-ecs-baf-part-1/) (Michele Caini, 블로그 연재)
- [여러분의 ECS는 아마 여전히 형편없을 겁니다](https://gist.github.com/Dreaming381/89d65f81b9b430ffead443a2d430defc) (Dreaming381, Gist)
- [희소 집합](https://www.geeksforgeeks.org/sparse-set/) (Geeks for Geeks)
- [Hibitset](https://docs.rs/hibitset/0.6.3/hibitset/) (DOCS.RS)

## 일반 질문

<a id="what-is-ecs"></a>

### ECS란 무엇인가요?

ECS("Entity Component System", 엔티티 컴포넌트 시스템)는 데이터와 동작을 분리해 코드 재사용성을 높이는 설계 방식입니다. 데이터는 흔히 CPU 캐시를 효율적으로 활용하는 방식으로 저장되며, 이는 성능 향상으로 이어집니다. ECS는 다음과 같은 특징을 가집니다.

- 엔티티는 고유한 식별자입니다.
- 컴포넌트는 동작이 없는 단순한 데이터 타입입니다.
- 엔티티는 컴포넌트를 0개 이상 가질 수 있습니다.
- 엔티티가 가진 컴포넌트는 동적으로 바뀔 수 있습니다.
- 시스템은 특정 컴포넌트 집합을 가진 엔티티에 연결되는 함수입니다.

ECS 설계 패턴은 보통 프레임워크를 통해 구현됩니다. "엔티티 컴포넌트 시스템"이라는 용어는 이 설계 패턴의 특정 구현체를 가리키는 데도 자주 사용됩니다.

<a id="when-is-something-an-ecs"></a>

### 어떤 조건을 갖춰야 ECS라고 할 수 있나요?

가장 엄격하게 해석하면, 앞선 질문의 정의에 맞는 엔티티, 컴포넌트, 시스템을 갖추고 있어야 ECS라고 할 수 있습니다.

실제로는 ECS라는 용어를 조금 더 넓게 사용합니다. 어떤 ECS 프레임워크는 시스템 없이 엔티티를 조회하는 메서드만 제공합니다. 다른 프레임워크는 컴포넌트가 아닌 것도 엔티티에 추가할 수 있게 합니다. 많은 사람은 이런 구현체도 ECS로 봅니다.

일반적으로 엔티티에 무언가를 추가할 수 있고, 특정한 것은 가지면서 다른 것은 가지지 않은 엔티티를 조회할 수 있는 프레임워크라면 ECS로 간주합니다.

<a id="why-is-ecs-used"></a>

### ECS를 사용하는 이유는 무엇인가요?

게임 개발자 사이에서 ECS가 인기를 얻는 데는 여러 이유가 있습니다.

- 일반적으로 더 많은 게임 오브젝트를 처리할 수 있습니다.
- 코드를 재사용하기 쉬운 편입니다.
- 새로운 기능을 추가해 코드를 확장하기 쉽습니다.
- 더 동적인 방식으로 코드를 작성할 수 있습니다.

<a id="who-is-using-ecs"></a>

### 어디에서 ECS를 사용하고 있나요?

여러 상용 프로젝트와 엔진에서 ECS를 사용하고 있거나 사용한 적이 있습니다. ECS를 사용하는 다른 프로젝트를 알고 있다면 알려 주세요!

- [Unity DOTS](https://unity.com/dots) (엔진)
- [Unreal (Sequencer)](https://www.unrealengine.com/en-US/tech-blog/performance-at-scale-sequencer-in-unreal-engine-4-26) (엔진)
- [Our Machinery](https://ourmachinery.com/) (엔진)
- [Photon Quantum](https://doc.photonengine.com/en-us/quantum/current/getting-started/quantum-intro) (엔진)
- [Bevy](https://bevyengine.org/) (엔진)
- [Amethyst](https://amethyst.rs/) (엔진)
- [Minecraft](https://minecraft.gamepedia.com/Bedrock_Edition) (게임, EnTT 사용)
- [Overwatch](https://playoverwatch.com/en-us/) (게임, 자체 ECS 사용)
- [Alan Wake 2](https://store.epicgames.com/en-US/p/alan-wake-2) (게임, [자체 ECS](https://gdcvault.com/play/1034295/ECS-in-Practice-The-Case) 사용)
- [Warhammer 40K: Space Marine 2](https://store.steampowered.com/app/2183900/Warhammer_40000_Space_Marine_2/) (게임, [자체 ECS](https://schedule.gdconf.com/session/the-ecs-behind-warhammer-40000-space-marine-2/906778) 사용)
- [Diablo II: Resurrected](https://diablo2.blizzard.com/) (게임, EnTT 사용)
- [Call of Duty: Vanguard](https://www.callofduty.com/vanguard) (게임, EnTT 사용)
- [Tiny Glade](https://store.steampowered.com/app/2198150/Tiny_Glade/) (게임, [Bevy ECS](https://bevyengine.org/) 사용)
- [Xenonauts](https://store.steampowered.com/app/223830/Xenonauts/) (게임, 자체 ECS 사용)
- [Survival City](https://play.google.com/store/apps/details?id=com.playstack.survivalcity&hl=en&gl=US) (게임, Entitas 사용)
- [Penko Park](https://store.steampowered.com/app/852090/Penko_Park) (게임, Entitas 사용)
- [Doomsday Vault](https://apps.apple.com/us/app/doomsday-vault/id1457773760) (게임, Entitas 사용)
- [Phantom Brigade](https://www.youtube.com/watch?v=uHsh9kWQeDc&ab_channel=BraceYourselfGames) (게임, Entitas 사용)
- [Minecraft Legends](https://www.minecraft.net/en-us/about-legends) (게임, EnTT 사용)
- [Minecraft Earth](https://www.minecraft.net/en-us/about-earth) (게임, EnTT 사용)
- [War of Rights](https://store.steampowered.com/app/424030/War_of_Rights/) (게임, EnTT 사용)
- [ArcGIS](https://developers.arcgis.com/arcgis-runtime/) (프레임워크, EnTT 사용)
- [Aether Engine](https://hadean.com/spatial-simulation/) (프레임워크, EnTT 사용)
- [FastSuite](https://www.fastsuite.com/) (시뮬레이션, EnTT 사용)
- [RoboCraft](https://robocraftgame.com/) (게임, Svelto 사용)
- [GameCraft](https://store.steampowered.com/app/1078000/Gamecraft/) (게임, Svelto 사용)
- [CardLife](http://cardlifegame.com/) (게임, Svelto 사용)
- [Hytale](https://hytale.com/) (게임, Flecs 사용)
- [Tempest Rising](https://store.steampowered.com/app/1486920/Tempest_Rising/) (게임, Flecs 사용)
- [Territory Control](https://store.steampowered.com/app/690290/Territory_Control_2/) (게임, Flecs 사용)
- [Resistance is Brutal](https://store.steampowered.com/app/3378140/Resistance_Is_Brutal/) (게임, Flecs 사용)
- [Age of Respair](https://store.steampowered.com/app/3164360/Age_of_Respair/) (게임, Flecs 사용)
- [Termination Shock](https://store.steampowered.com/app/2510820/Extermination_Shock/) (게임, Flecs 사용)
- [The Forge](https://github.com/ConfettiFX/The-Forge) (렌더링 엔진, Flecs 사용)
- [Bebylon](https://bebylon.world/) (게임, Flecs 사용)
- [Sol Survivor](https://nicok.itch.io/sol-survivor-demo) (게임, Flecs 사용)

<a id="how-is-ecs-different-from-oop"></a>

### ECS와 객체 지향 프로그래밍은 어떻게 다른가요?

ECS는 객체 지향 프로그래밍(OOP)의 대안으로 자주 소개됩니다. ECS와 OOP에는 겹치는 부분이 있지만, 애플리케이션 설계에 영향을 주는 차이점이 있습니다.

- OOP에서는 상속이 핵심적인 위치를 차지하고, ECS에서는 조합이 핵심적인 위치를 차지합니다.
- OOP는 데이터의 캡슐화를 권장하고, ECS는 데이터를 공개하는 POD(plain old data) 객체를 권장합니다.
- OOP는 데이터와 동작을 한곳에 두고, ECS는 데이터와 동작을 분리합니다.
- OOP의 객체 인스턴스는 하나의 정적 타입을 가지지만, ECS의 엔티티는 동적으로 바뀌는 여러 컴포넌트를 가질 수 있습니다.

ECS가 *객체 지향 설계*의 특성에 부합하므로 그 하위 집합으로 봐야 한다는 주장도 있습니다. 관련 내용은 https://www.gamedev.net/blogs/entry/2265481-oop-is-dead-long-live-oop/ 를 참고하세요.

다만 실제 ECS 애플리케이션을 설계하는 과정은 대부분의 사람이 OOP로 인식하는 방식과 상당히 다릅니다. 따라서 적어도 별도의 설계 방식으로 접근하는 편이 유용합니다.

<a id="how-is-ecs-different-from-entity-component-frameworks"></a>

### ECS와 엔티티-컴포넌트 프레임워크는 어떻게 다른가요?

혼동하기 쉽지만 ECS와 엔티티-컴포넌트(EC) 프레임워크는 서로 다릅니다. 게임 엔진에서 흔히 사용하는 EC 프레임워크도 엔티티를 만들고 컴포넌트를 조합할 수 있다는 점에서는 ECS와 비슷합니다. 하지만 EC 프레임워크의 컴포넌트는 데이터와 동작을 모두 담는 클래스이며, 동작은 컴포넌트에서 직접 실행됩니다.

간단한 EC 프레임워크는 다음과 같은 형태입니다.

```cpp
class IComponent {
public:
    virtual void update() = 0;
};

class Entity {
    vector<IComponent*> components;
public:
    void addComponent(IComponent *component);
    void removeComponent(IComponent *component);
    void updateComponents();
};
```

EC 프레임워크에서 기능을 구현할 때는 일반적으로 `IComponent` 인터페이스를 상속하고, 여러 컴포넌트를 조합해 엔티티를 구성합니다. Unity의 `GameObject` 시스템이 실제 EC의 예입니다.

<a id="is-ecs-hard-to-learn"></a>

### ECS는 배우기 어려운가요?

ECS를 이루는 개념과 규칙은 수가 적어 대체로 배우기 쉽습니다. 하지만 올바르게 적용하려면 연습이 필요할 수 있습니다. 특히 OOP에 익숙하다면 ECS 설계의 일부가 직관에 어긋나게 느껴질 수 있습니다.

경험담에 따르면, ECS를 어느 순간 확실히 이해하고 나면 코드를 작성하고 재사용하며 규모를 확장하기가 더 쉬워졌다는 사용자들이 있습니다.

<a id="is-ecs-a-lower-level-of-abstraction"></a>

### ECS는 추상화 수준이 더 낮은가요?

반드시 그렇지는 않습니다. 어떤 ECS 설계는 저수준 하드웨어 최적화를 활용할 수 있지만, ECS용으로 작성하는 코드 자체가 다른 방식보다 반드시 더 저수준이거나 고수준인 것은 아닙니다.

<a id="does-ecs-require-writing-more-code"></a>

### ECS를 사용하면 코드를 더 많이 작성해야 하나요?

하나의 답으로 정리하기는 어렵습니다. 사용하는 ECS 프레임워크와 엔진에 크게 좌우됩니다.

ECS 프레임워크가 엔진에 통합되어 있으면 매우 짧고 간결한 코드를 작성할 수 있으며, ECS를 쓰지 않는 방식보다 코드가 더 짧아질 수도 있습니다. ECS가 통합된 엔진으로는 Bevy, Amethyst, Our Machinery 등이 있습니다.

ECS가 엔진에 통합되어 있지 않다면, 엔진 고유의 타입과 ECS 사이를 연결하는 추가 코드 때문에 작성해야 할 코드가 늘어날 수 있습니다.

그렇더라도 ECS 코드를 작성하는 데 들인 시간은 유지보수가 쉬워지면서 절약하는 시간으로 상쇄될 수 있습니다.

<a id="is-ecs-good-for-low-level-code"></a>

### ECS는 저수준 코드에 적합한가요?

렌더링이나 물리 연산 같은 저수준 엔진 코드는 캐시 지역성을 최적화하면서 벡터화 같은 하드웨어의 고급 기능을 활용해야 할 수 있습니다. 일부 ECS 프레임워크는 다른 프레임워크보다 이런 작업에 더 적합합니다.

일반적으로 컴포넌트의 원시 배열에 직접 접근할 수 있는 ECS가 저수준 최적화에 더 유리합니다. 특히 현대적인 게임에서는 이러한 시스템을 얼마나 쉽게 멀티스레드로 실행할 수 있는지도 중요한 판단 기준입니다.

<a id="can-ecs-be-implemented-in-any-language"></a>

### 어떤 언어로든 ECS를 구현할 수 있나요?

네.

<a id="should-i-write-my-own-ecs"></a>

### ECS를 직접 만들어야 할까요?

ECS는 개념과 규칙이 적기 때문에 동작하는 구현체를 만드는 것 자체는 어렵지 않습니다. 원하는 새 기능을 자유롭게 추가하거나 꼭 필요한 기능만 구현할 수 있다는 등 직접 만드는 데는 여러 장점이 있습니다.

다만 직접 구현한다면, 기존의 검증된 구현체보다 성능이 뛰어나지는 않을 것이라고 예상해야 합니다. 다양한 ECS 연산에서 균형 잡힌 성능을 내기 위해 오랜 시간에 걸쳐 많은 기법이 개발되었습니다. 새로운 발전을 따라가려면 꾸준히 배우고 실험하며 개선해야 합니다.

많은 일이 그렇듯, ECS 구현도 시작하기는 쉽지만 능숙해지기는 어렵습니다.

<a id="is-ecs-fast"></a>

### ECS는 빠른가요?

일반적으로는 그렇습니다. 물론 무엇을 측정하는지, 어떤 ECS 구현체인지에 따라 달라집니다. 구현체마다 선택하는 절충점이 다르기 때문에, 한 프레임워크에서 매우 빠른 연산이 다른 프레임워크에서는 상당히 느릴 수도 있습니다.

ECS 구현체는 대체로 엔티티 집합을 조회하고 순차적으로 순회하거나, 실행 중에 컴포넌트를 동적으로 바꾸는 작업에 강합니다. 반면 이진 트리나 공간 자료 구조처럼 특화된 자료 구조가 필요한 쿼리나 연산에는 대체로 강하지 않습니다. 구현체의 절충점을 이해하고 그 설계에 맞게 활용해야 ECS에서 최대한의 성능을 얻을 수 있습니다.

<a id="is-ecs-code-more-reusable"></a>

### ECS 코드는 재사용하기 더 쉬운가요?

네. ECS의 동작은 컴포넌트 집합을 기준으로 연결되기 때문입니다. 예를 들어 OOP에서 동작이 특정 클래스에 강하게 결합되는 것과는 차이가 있습니다. 여기에는 몇 가지 의미가 있습니다.

첫 번째는 분명합니다. 동작이 하나의 클래스에 묶이지 않으므로 서로 다른 종류의 엔티티에서 재사용할 수 있습니다. 대표적인 예는 `Position`과 `Velocity` 컴포넌트를 가진 모든 엔티티에 적용되는 `Move` 시스템입니다.

OOP에 가까운 다른 설계에서도 이런 구성이 불가능한 것은 아니지만, 클래스 상속에 의존하는 경우가 많습니다. 상속에는 클래스 계층 구조를 리팩터링하기 어렵고, 하위 계층의 기반 클래스에 시간이 지나면서 불필요한 기능이 쌓이기 쉽다는 등의 잘 알려진 문제가 있습니다.

EC 프레임워크도 게임 엔티티에 컴포넌트를 추가하는 방식으로 비슷한 수준의 재사용성을 제공할 수 있습니다. [ECS와 엔티티-컴포넌트 프레임워크는 어떻게 다른가요?](#how-is-ecs-different-from-entity-component-frameworks)를 참고하세요.

여기서 ECS의 큰 장점은 개발의 어느 단계에서든 새 시스템을 추가할 수 있고, 알맞은 컴포넌트를 가진 기존 엔티티와 새 엔티티에 그 시스템이 자동으로 연결된다는 점입니다. 이는 시스템을 하나의 책임을 가진 작은 기능 단위로 개발하고, 서로 다른 프로젝트에서 쉽게 활용하는 설계를 장려합니다.

<a id="is-ecs-good-for-multithreading"></a>

### ECS는 멀티스레딩에 적합한가요?

일반적으로는 그렇습니다. 데이터와 동작이 분리되어 있으면 각 시스템과 그 의존성, 실행 일정을 파악하기 쉽습니다. 멀티스레딩을 구현하는 구체적인 방식은 ECS 구현체마다 다르지만, 대부분 코드를 멀티스레드로 실행하기 쉽게 해 줍니다.

<a id="can-ecs-be-used-outside-of-gaming"></a>

### 게임 이외의 분야에서도 ECS를 사용할 수 있나요?

네. 게임 이외의 프로젝트에도 사용할 수 있으며, 실제 사용 사례도 있습니다.

<a id="how-do-i-start-with-ecs"></a>

### ECS를 어떻게 시작하면 좋을까요?

기존 ECS 자료를 읽고, 자료에서 설명하는 방식을 직접 실험해 보는 것을 적극 권장합니다. ECS 예제 프로젝트의 코드를 읽는 것도 ECS 애플리케이션을 어떻게 작성하는지 빠르게 이해하는 좋은 방법입니다.

<a id="how-do-i-design-for-ecs"></a>

### ECS에 맞게 설계하려면 어떻게 해야 하나요?

ECS 애플리케이션의 설계는 게임 데이터를 담는 컴포넌트, 즉 자료 구조를 만드는 데서 시작합니다. 다음 사항을 중요하게 고려해야 합니다.

- 데이터 인스턴스가 얼마나 많이 존재할 것인가
- 데이터에 얼마나 자주 접근하는가
- 데이터가 얼마나 자주 변경되는가
- 언제 데이터에 접근하거나 데이터를 변경해야 하는가
- 어떤 데이터에 함께 접근하거나 어떤 데이터를 함께 변경하는가
- 데이터의 카디널리티, 즉 서로 다른 값의 개수는 얼마인가

컴포넌트와 시스템이 하나의 책임을 갖도록 설계하는 것이 좋습니다. 이렇게 하면 프로젝트 간에 재사용하기 쉽고 코드 리팩터링도 쉬워집니다.

<a id="what-are-the-different-ways-to-implement-an-ecs"></a>

### ECS를 구현하는 방식에는 어떤 것들이 있나요?

ECS를 구현하는 방식은 다양하며, 각각 장단점이 다릅니다. 다음은 그중 널리 쓰이는 몇 가지 방식입니다. 모든 방식을 나열한 목록은 아닙니다.

<a id="archetypes-aka-dense-ecs-or-table-based-ecs"></a>

#### 아키타입 방식("밀집 ECS" 또는 "테이블 기반 ECS")

아키타입 ECS는 엔티티를 테이블에 저장합니다. 컴포넌트는 열에, 엔티티는 행에 해당합니다. 아키타입 구현체는 조회와 순회가 빠릅니다.

아키타입 방식의 구현체로는 [Flecs](https://github.com/SanderMertens/flecs), [Our Machinery](https://ourmachinery.com/), [Unity DOTS](https://unity.com/dots), [Unreal Sequencer](https://www.unrealengine.com/en-US/tech-blog/performance-at-scale-sequencer-in-unreal-engine-4-26), [Unreal Mass](https://docs.unrealengine.com/5.0/en-US/overview-of-mass-entity-in-unreal-engine/), [Bevy ECS](https://bevyengine.org/), [Legion](https://github.com/amethyst/legion), [Hecs](https://github.com/Ralith/hecs), [Ark](https://github.com/mlange-42/ark)가 있습니다.

<a id="sparse-set-ecs-aka-sparse-ecs"></a>

#### 희소 집합 기반 ECS("희소 ECS")

희소 집합 기반 ECS는 컴포넌트 타입마다 별도의 희소 집합을 두고, 엔티티 ID를 키로 사용해 각 컴포넌트를 저장합니다. 희소 집합 구현체는 추가와 삭제 연산이 빠릅니다.

희소 집합 방식의 구현체로는 [EnTT](https://github.com/skypjack/entt)와 [Shipyard](https://github.com/leudz/shipyard)가 있습니다.

<a id="bitset-based-ecs"></a>

#### 비트셋 기반 ECS

비트셋 기반 ECS는 엔티티 ID를 인덱스로 사용하는 배열에 컴포넌트를 저장하고, 비트셋으로 각 엔티티가 특정 컴포넌트를 갖는지 표시합니다. 비트셋 기반 접근법에도 여러 변형이 있습니다. 한 가지는 컴포넌트 타입마다 배열과 비트셋을 두고, 해당 컴포넌트를 가진 엔티티를 비트셋으로 표시하는 방식입니다. 또 다른 방식은 [hibitset](https://docs.rs/hibitset/0.6.3/hibitset/) 자료 구조를 사용합니다.

비트셋 방식의 구현체로는 [EntityX](https://github.com/alecthomas/entityx)와 [Specs](https://github.com/amethyst/specs)가 있습니다.

<a id="reactive-ecs"></a>

#### 반응형 ECS

반응형 ECS는 엔티티가 변경될 때 발생하는 신호를 이용해 어떤 엔티티가 시스템이나 쿼리의 조건에 맞는지 추적합니다.

반응형 ECS의 예로는 [Entitas](https://github.com/sschmid/Entitas-CSharp)가 있습니다.

<a id="how-are-components-modified"></a>

### 컴포넌트는 어떻게 수정하나요?

ECS에서 컴포넌트를 수정하는 방법은 보통 두 가지입니다. 하나의 엔티티에서 컴포넌트를 수정하거나, 시스템 안에서 여러 엔티티의 컴포넌트 값을 수정합니다.

첫 번째 방식의 예입니다.

```cpp
entity.set<Position>({10, 20});
```

두 번째 방식의 예입니다.

```cpp
system<Position, Velocity>().each(
    [](entity e, Position& p, Velocity & v) {
        p.x += v.x;
        p.y += v.y;
    });
```

두 번째 방식은 조회 횟수가 적고 효율적인 컴포넌트 저장 방식을 활용할 수 있어 일반적으로 더 빠릅니다.

<a id="how-are-entities-matched-with-systems"></a>

### 엔티티와 시스템은 어떻게 연결되나요?

널리 사용되는 구현 방식은 세 가지입니다.

1. 아키타입 기반 ECS에서는 쿼리가 조건에 맞는 테이블 목록을 저장합니다. 각 테이블에는 여러 엔티티가 들어갈 수 있습니다. 테이블 구성은 빠르게 안정되므로, 쿼리 평가의 오버헤드가 평균적으로 0까지 줄어든다는 장점이 있습니다.

2. 희소 집합 기반 ECS에서는 조회 대상 컴포넌트 중 하나에 속한 모든 엔티티를 순회합니다. 보통 엔티티 수가 가장 적은 컴포넌트를 선택하고, 각 엔티티가 나머지 컴포넌트도 가지고 있는지 차례로 검사합니다. 비트셋 기반 ECS 구현체도 비슷한 방식을 사용합니다.

3. 반응형 ECS에서는 시스템이 엔티티의 조건 충족 여부를 바꿀 수 있는 신호를 수신하고, 알맞은 컴포넌트 집합을 가진 엔티티를 모읍니다.

<a id="what-are-entity-relationships"></a>

### 엔티티 관계란 무엇인가요?

엔티티 관계는 컴포넌트뿐 아니라 두 요소의 쌍도 엔티티에 추가할 수 있도록 ECS 모델을 확장한 것입니다. 간단한 관계는 다음과 같이 나타낼 수 있습니다.

```c
alice.add<Likes>(bob);
```

이 예에서 `Likes, bob`이 쌍이며, `Likes`는 관계의 종류, `bob`은 관계의 대상입니다. `alice`와 `bob`은 모두 일반적인 엔티티입니다. 엔티티 관계에 대한 자세한 내용은 [이 글](https://ajmmertens.medium.com/building-games-in-ecs-with-entity-relationships-657275ba2c6c)을 참고하세요.

## 활용 방법

<a id="how-to-create-a-hierarchy-in-ecs"></a>

### ECS에서 계층 구조는 어떻게 만드나요?

ECS에서 계층 구조를 구현하는 방법은 여러 가지이며, 어떤 방법을 사용할 수 있는지는 ECS 구현체에 따라 다릅니다. 모든 구현체에서 사용할 수 있는 한 가지 방법은 다음과 같이 컴포넌트에 계층 정보를 저장하는 것입니다.

```c
// 자식 엔티티에 부모 엔티티를 저장합니다
struct Parent {
    entity parent;
};

// 벡터를 가진 컴포넌트에 부모의 모든 자식을 저장합니다
struct Children {
    vector<entity> children;
};

// 연결 리스트에 자식들을 저장합니다
struct ChildList {
    entity first_child; // 엔티티의 첫 번째 자식
    entity prev_sibling; // 이전 형제
    entity next_sibling; // 다음 형제
};
```

이 방식은 컴포넌트 조회에 의존하므로 계층 구조를 순회하는 시스템이 느려질 수 있다는 단점이 있습니다. 유연하기는 하지만, 트랜스폼 적용 같은 저수준 시스템에는 이상적이지 않습니다.

애플리케이션이 계층 구조를 위에서 아래로 순회하기만 하면 된다면, 계층의 깊이에 따라 엔티티를 정렬하는 방식이 특히 잘 맞습니다. 순회가 빠르다는 장점이 있지만 자주 정렬해야 한다는 단점이 있습니다.

아키타입 ECS 프레임워크에서는 하위 트리를 서로 다른 테이블로 나눌 수 있는 경우도 있습니다. 테이블이나 하위 트리를 깊이에 따라 정렬할 수 있습니다. 순회가 빠르고 정렬을 자주 하지 않아도 된다는 장점이 있습니다. 반면 작은 테이블이 많이 생겨 성능이 저하될 수 있습니다.

<a id="how-to-store-spatial-data-in-ecs"></a>

### ECS에서 공간 데이터는 어떻게 저장하나요?

쿼드트리나 옥트리 같은 공간 자료 구조는 보통 ECS에 직접 저장하지 않습니다. 이런 구조의 데이터 배치가 일반적인 ECS의 데이터 배치와 잘 맞지 않기 때문입니다.

ECS와 함께 정밀 단계(narrow phase)의 공간 쿼리를 처리할 때 효과적인 한 가지 방법은, 매 프레임의 시작이나 끝에 관련 엔티티를 순회해 공간 자료 구조에 저장하는 쿼리를 만드는 것입니다.

광역 단계(broad phase)의 공간 쿼리에는 ECS가 지원한다면 런타임 태그를 활용할 수 있습니다. 각 태그가 공간 격자의 셀 하나에 대응하도록 하고, 해당 태그를 조건으로 조회하면 특정 영역에 없는 엔티티들을 대량으로 빠르게 제외할 수 있습니다.

## 데이터 지향 설계

<a id="what-is-data-oriented-design"></a>

### 데이터 지향 설계란 무엇인가요?

위키백과는 데이터 지향 설계(Data Oriented Design, DoD)를 다음과 같이 정의합니다.

> … 비디오 게임 개발에서 사용하는 프로그램 최적화 접근법으로, CPU 캐시를 효율적으로 사용하는 데서 출발한다. 데이터 배치에 집중하고, 필드가 필요한 시점에 따라 필드를 분리하고 정렬하며, 데이터의 변환을 중심으로 사고하는 방식이다.

데이터 지향 설계는 다양한 기법과 각 기법을 적용할 조건을 포괄하는 용어입니다. 일반적인 목표는 애플리케이션에 있는 여러 종류의 데이터가 어떤 패턴으로 접근되는지 분석하고, _그 접근 패턴에 맞춰_ 하드웨어를 최적으로 활용하는 자료 구조를 선택하는 것입니다. 하드웨어 최적화는 주로 CPU 캐시 사용을 최적화하고, RAM 읽기와 쓰기(캐시 미스)를 줄이며, SIMD 명령어를 활용하는 것과 관련이 있지만 여기에만 한정되지는 않습니다.

데이터 지향 설계를 폭넓게 다루는 자료로 Richard Fabian의 책 "Data Oriented Design"이 있습니다.
[https://www.dataorienteddesign.com/dodbook/](https://www.dataorienteddesign.com/dodbook/)

<a id="is-ecs-the-same-as-dod"></a>

### ECS와 데이터 지향 설계는 같은 것인가요?

아니요. ECS가 아니어도 데이터 지향 설계 원칙을 따르는 코드를 작성할 수 있고, 데이터 지향 설계를 활용하지 않는 ECS를 만들 수도 있습니다.

ECS 패턴은 데이터 지향 설계에 잘 맞습니다. 그래서 모든 프레임워크가 그런 것은 아니지만, 많은 ECS 프레임워크는 애플리케이션이 데이터 지향 설계의 최적화를 활용할 수 있도록 저장 구조를 설계합니다.

ECS가 연속된 컴포넌트 배열을 순회한다면 데이터 지향 설계의 원칙과 최적화를 활용할 수 있습니다.

<a id="what-is-random-access-memory"></a>

### RAM이란 무엇인가요?

RAM(Random Access Memory)은 컴퓨터가 일반적으로 많은 용량을 갖추고 있는 메모리로, 애플리케이션의 전체 상태와 코드가 저장되는 곳입니다. CPU는 애플리케이션 코드를 실행할 때 RAM과 데이터를 주고받습니다.

RAM 자체는 절대적인 기준으로 보면 매우 빠르지만, 데이터를 많이 다루는 애플리케이션에서는 CPU와 RAM 사이의 버스가 병목이 될 수 있습니다. 따라서 데이터 지향 설계에서는 RAM에서 데이터를 읽어오는 횟수를 최소화하는 기법을 사용합니다.

<a id="what-is-a-cpu-cache"></a>

### CPU 캐시란 무엇인가요?

CPU 캐시는 RAM보다 훨씬 빠르지만 용량도 _훨씬_ 작은 메모리입니다. CPU가 RAM에서 데이터를 읽어오면 그 데이터는 캐시 계층에 저장됩니다. 낮은 레벨의 캐시가 더 빠르고, 용량이 큰 캐시 계층은 더 느립니다.

데이터 지향 설계는 CPU 캐시를 최대한 효율적으로 활용해 RAM에서 데이터를 읽어오는 횟수를 최소화하는 기법을 사용합니다.

<a id="what-is-a-cache-line"></a>

### 캐시 라인이란 무엇인가요?

캐시 라인은 RAM에서 한 번에 읽어오는 데이터의 단위입니다. 예를 들어 애플리케이션이 RAM에서 4바이트를 요청해도, CPU는 실제로 요청한 주소가 포함된 64바이트를 읽어옵니다.

애플리케이션은 서로 가까운 위치에 데이터를 저장해 RAM에서 읽어오는 횟수를 줄일 수 있습니다. 이후 연산에 필요한 데이터가 이미 캐시에 들어 있을 가능성이 높아지기 때문입니다.

<a id="what-is-locality-of-reference"></a>

### 참조 지역성이란 무엇인가요?

지역성은 시간적 지역성과 공간적 지역성으로 나뉩니다. 시간적 지역성은 짧은 시간 안에 같은 데이터를 다시 사용하는 성질입니다. 공간적 지역성은 데이터의 저장 위치가 서로 가까운 성질입니다. 어느 쪽이든 지역성이 높으면 CPU가 접근 패턴을 더 잘 예측할 수 있어 캐시 효율이 높아집니다.

<a id="what-is-simd"></a>

### SIMD란 무엇인가요?

SIMD(Single Instruction Multiple Data)는 명령어 하나를 실행하는 동안 여러 값을 처리할 수 있는 명령어 또는 명령어 계열을 가리킵니다.

<a id="what-is-vectorization"></a>

### 벡터화란 무엇인가요?

벡터화 또는 자동 벡터화는 특정 조건을 만족하는 코드가 SIMD 명령어를 사용해 성능을 높이는 과정입니다. 이런 최적화된 명령어를 사용하면 벡터화된 코드가 일반 코드보다 몇 배 빠르게 실행될 수 있습니다.

벡터화할 수 있는 코드의 조건은 다음과 같습니다.

- 데이터가 배열처럼 연속된 공간에 저장되어 있어야 합니다.
- 코드에 분기나 함수 호출이 없어야 합니다.

컴파일러는 일반적으로 위 조건을 만족하는 루프를 벡터화할 수 있습니다. 다만 어떤 경우에 자동으로 벡터화되는지는 컴파일러에 따라 다릅니다. [이 페이지](https://llvm.org/docs/Vectorizers.html)는 Clang 컴파일러가 자동으로 벡터화할 수 있는 경우를 정리합니다.

<a id="what-is-false-sharing"></a>

### 거짓 공유란 무엇인가요?

거짓 공유(false sharing)는 서로 다른 스레드가 같은 캐시 라인에 있는 서로 다른 값을 읽고 수정하려 할 때 발생합니다. 이로 인해 캐시 동기화가 발생하며 성능이 저하될 수 있습니다.

서로 다른 스레드가 접근하는 데이터가 하나의 캐시 라인에 함께 들어오지 않도록 충분히 떨어뜨려 저장하면 거짓 공유를 피할 수 있습니다.

<a id="what-is-aos"></a>

### AoS란 무엇인가요?

AoS(Array of Structs, 구조체의 배열)는 여러 필드를 가진 구조체를 배열로 저장하는 메모리 배치입니다. 예는 다음과 같습니다.

```c
struct AoS {
  int m_1;
  int m_2;
};

AoS values[1000];
```

AoS는 데이터를 배열로 저장하므로 캐시 지역성에 유리합니다. 하지만 코드에서 구조체의 일부 멤버만 필요해도 실제로 필요한 양보다 더 많은 데이터가 캐시에 로드될 수 있다는 단점이 있습니다.

ECS에서 AoS는 보통 모든 컴포넌트를 같은 배열에 저장하는 메모리 배치를 가리킵니다.

<a id="what-is-soa"></a>

### SoA란 무엇인가요?

SoA(Struct of Arrays, 배열의 구조체)는 구조체가 필드별로 하나씩 여러 배열을 갖는 메모리 배치입니다. 예는 다음과 같습니다.

```c
struct SoA {
  int m_1[1000];
  int m_2[1000];
};

SoA values;
```

AoS와 마찬가지로 데이터를 배열에 저장하므로 캐시 지역성에 유리합니다. 여기에 더해 구조체의 일부 멤버만 필요한 경우 다른 멤버는 캐시에 로드하지 않는다는 장점이 있습니다. 반면 코드에서 다른 멤버에 불규칙하게 접근해야 한다면 AoS보다 캐시 미스가 더 많이 발생할 수 있습니다.

ECS에서 SoA는 보통 컴포넌트들을 별도의 배열에 저장하는 메모리 배치를 가리킵니다.

<a id="what-is-branch-prediction"></a>

### 분기 예측이란 무엇인가요?

CPU는 명령어를 실행할 때 `if-else`나 `switch` 같은 조건문이 어떻게 평가될지 추정해 코드가 어느 경로로 진행할지 예측합니다. 해당 경로의 명령어를 명령어 캐시에 미리 로드하며, 미리 실행하기도 합니다.

코드에 예측하기 어려운 분기가 많으면 분기 예측기가 미리 계산한 결과를 자주 버려야 할 수 있으며, 그만큼 코드 실행이 느려집니다.

## 용어집

<a id="entity"></a>

### 엔티티(Entity)

ECS에서 엔티티는 게임 속 하나의 대상을 나타내며, 일반적으로 고유한 정숫값으로 표현됩니다.

<a id="component"></a>

### 컴포넌트(Component)

컴포넌트는 엔티티에 추가하거나 엔티티에서 제거할 수 있는 데이터 타입입니다. ECS의 컴포넌트는 일반적으로 캡슐화되지 않은 단순한 데이터 타입입니다.

<a id="tag"></a>

### 태그(Tag)

태그는 데이터가 없는 컴포넌트입니다.

<a id="system"></a>

### 시스템(System)

시스템은 특정 컴포넌트 집합을 가진 모든 엔티티에 연결되는 실행 가능한 객체입니다.

<a id="query"></a>

### 쿼리(Query)

쿼리는 시스템과 비슷하지만, 그 자체로 실행할 수는 없습니다.

<a id="world"></a>

### 월드(World)

월드는 모든 ECS 데이터를 담는 컨테이너입니다. ECS 프레임워크는 하나의 애플리케이션에 여러 ECS 월드를 만들 수 있게 하는 경우가 많습니다.

<a id="registry"></a>

### 레지스트리(Registry)

월드와 같은 의미입니다.

<a id="archetype"></a>

### 아키타입(Archetype)

특정 컴포넌트 집합에 속하는 엔티티들을 저장하는 자료 구조입니다. 컴포넌트는 열 단위로 연속된 배열에 저장됩니다.

<a id="table"></a>

### 테이블(Table)

아키타입과 같은 의미로 자주 사용됩니다. 일부 ECS 구현체에서는 테이블이 "밀집" 컴포넌트, 즉 인덱스가 서로 대응하는 연속 배열에 저장된 컴포넌트를 담는 아키타입을 뜻합니다. 이때 아키타입은 "희소" 컴포넌트, 즉 연속적으로 저장되지 않거나 인덱스가 서로 대응하지 않는 컴포넌트와 테이블을 참조하는 인덱스를 저장합니다.

<a id="sparse-set"></a>

### 희소 집합(Sparse set)

순회, 조회, 삽입, 삭제가 빠른 자료 구조입니다. 해시 맵과 비슷하지만 연속된 식별자를 다루는 데 더 적합합니다.
