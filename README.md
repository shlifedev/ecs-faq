한국어 | [English](README.en.md)

# 엔티티 컴포넌트 시스템 FAQ

이 FAQ는 ECS(Entity Component System)와 고성능 게임을 만드는 최신 개발 방식에 관심 있는 분들을 위한 문서입니다. 각 답변은 모든 내용을 상세히 다루기보다, 핵심을 짧고 정확하게 전달하는 데 초점을 맞췄습니다. 더 깊이 알아보고 싶다면 [참고 자료](#resources)를 확인하세요.

이 문서는 [Sander Mertens의 ECS FAQ](https://github.com/SanderMertens/ecs-faq)를 한국어로 옮긴 번역본입니다. 아래 저자 소개와 본문에서 말하는 ‘저’는 원저자 Sander Mertens를 뜻합니다. [영어 원문](README.en.md)도 함께 제공합니다.

빠진 내용이나 잘못된 부분을 발견하면 언제든 이슈나 PR로 알려 주세요!

## 저자 소개

저는 C와 C++용 엔티티 컴포넌트 시스템인 [Flecs](https://github.com/SanderMertens/flecs)를 만든 개발자입니다. ECS의 기능을 더 잘 구현할 방법을 꾸준히 실험하고, 틈틈이 그 내용을 글로 정리합니다. ECS에 관해 이야기 나누고 싶다면 [Discord에 참여해 주세요](https://discord.gg/BEzP5Rgrrp)!

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
- [ECS를 직접 구현하는 게 좋을까요?](#should-i-write-my-own-ecs)
- [ECS는 빠른가요?](#is-ecs-fast)
- [ECS 코드는 재사용하기 더 쉬운가요?](#is-ecs-code-more-reusable)
- [ECS는 멀티스레딩에 적합한가요?](#is-ecs-good-for-multithreading)
- [게임 이외의 분야에서도 ECS를 사용할 수 있나요?](#can-ecs-be-used-outside-of-gaming)
- [ECS를 어떻게 시작하면 좋을까요?](#how-do-i-start-with-ecs)
- [ECS 기반 애플리케이션은 어떻게 설계하나요?](#how-do-i-design-for-ecs)
- [ECS를 구현하는 방식에는 어떤 것들이 있나요?](#what-are-the-different-ways-to-implement-an-ecs)
- [컴포넌트는 어떻게 수정하나요?](#how-are-components-modified)
- [시스템은 처리할 엔티티를 어떻게 찾나요?](#how-are-entities-matched-with-systems)
- [엔티티 관계란 무엇인가요?](#what-are-entity-relationships)

## 활용 방법

- [ECS에서 계층 구조는 어떻게 만드나요?](#how-to-create-a-hierarchy-in-ecs)
- [ECS에서 공간 데이터는 어떻게 저장하나요?](#how-to-store-spatial-data-in-ecs)

## 데이터 지향 설계

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

아래는 ECS 구현체와 ECS 패턴을 도입한 엔진의 목록입니다. 소스 코드 공개 여부와 관계없이 소개합니다. 원문에서는 최근 1년간 소스 코드에 의미 있는 변경이 없었던 프로젝트를 이 목록에서 제외하고 있습니다.

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

### 최근 활동이 없는 프로젝트

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

- [엔티티 컴포넌트 시스템 아키텍처, 제대로 살펴보기](https://www.youtube.com/watch?v=VpiprNBEZsk) (@alice-i-cecile, YouTube)
- [엔티티 컴포넌트 시스템 패턴의 동시성: 이론과 실제](https://www.youtube.com/watch?v=z2vqiVtmsuY) (Patrick Redmond, Jonathan Castello, José Manuel Calderón Trilla, Lindsey Kuper, YouTube)
- [오버워치의 게임플레이 아키텍처와 넷코드](https://www.youtube.com/watch?v=W3aieHjyNvw) (Blizzard, GDC)
- [데이터 지향 설계와 C++](https://www.youtube.com/watch?v=rX0ItVEVjHc) (Mike Acton, CppCon)
- [실용적인 최적화](https://www.youtube.com/watch?v=NAVbI1HIzCE) (Jason Booth, YouTube)
- [게임 개발에 Rust 활용하기](https://www.youtube.com/watch?v=aKLntZcp27M) (Catherine West, RustConf)
- [CPU 캐시란 무엇이며, 왜 알아야 할까](https://vimeo.com/97337258) (Scott Meyers, NDC)
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
- [ECS와 Entitas의 기반 개념을 엄밀하게 정의하기](https://medium.com/@icex33/formalisation-of-concepts-behind-ecs-and-entitas-8efe535d9516) (Maxim Zaks, 블로그)
- [엔티티 컴포넌트 시스템과 렌더링](https://ourmachinery.com/post/ecs-and-rendering/) (Our Machinery, 블로그)
- [Specs와 Legion: 서로 크게 다른 두 ECS 구현 방식](https://csherratt.github.io/blog/posts/specs-and-legion/) (Cora Sherrat, 블로그)
- [내 엔티티와 컴포넌트는 어디에 있을까](https://ajmmertens.medium.com/building-an-ecs-1-where-are-my-entities-and-components-63d07c7da742) (Sander Mertens, 블로그)
- [아키타입과 벡터화](https://medium.com/@ajmmertens/building-an-ecs-2-archetypes-and-vectorization-fe21690805f9) (Sander Mertens, 블로그)
- [엔티티 관계로 게임 만들기](https://ajmmertens.medium.com/building-games-in-ecs-with-entity-relationships-657275ba2c6c) (Sander Mertens, 블로그)
- [이제 게임을 데이터베이스로 바라봐야 하는 이유](https://ajmmertens.medium.com/why-it-is-time-to-start-thinking-of-games-as-databases-e7971da33ac3) (Sander Mertens, 블로그)
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

ECS(Entity Component System, 엔티티 컴포넌트 시스템)는 데이터와 동작을 분리해 코드를 재사용하기 쉽게 만드는 설계 방식입니다. 데이터를 저장할 때 CPU 캐시를 효율적으로 활용하도록 배치하는 경우가 많아 성능 면에서도 이점이 있습니다. ECS의 특징은 다음과 같습니다.

- 엔티티(Entity)는 고유한 식별자입니다.
- 컴포넌트(Component)는 동작을 포함하지 않는 단순한 데이터 타입입니다.
- 엔티티는 컴포넌트를 하나도 갖지 않을 수도 있고, 여러 개 가질 수도 있습니다.
- 엔티티를 구성하는 컴포넌트는 실행 중에 바뀔 수 있습니다.
- 시스템(System)은 특정 컴포넌트들을 가진 엔티티를 대상으로 실행되는 함수입니다.

ECS 설계 패턴은 보통 이를 지원하는 프레임워크를 통해 사용합니다. ‘엔티티 컴포넌트 시스템’이라는 말로 이 패턴을 구현한 특정 프레임워크를 가리키기도 합니다.

<a id="when-is-something-an-ecs"></a>

### 어떤 조건을 갖춰야 ECS라고 할 수 있나요?

엄격한 의미의 ECS라면 앞에서 정의한 엔티티, 컴포넌트, 시스템을 모두 갖춰야 합니다.

실제로는 ECS라는 말을 좀 더 넓은 의미로 사용합니다. 시스템을 따로 제공하지 않고 엔티티를 조회하는 메서드만 제공하는 프레임워크도 있고, 컴포넌트 외의 요소를 엔티티에 추가할 수 있는 프레임워크도 있습니다. 이런 구현체도 ECS로 보는 사람이 많습니다.

일반적으로는 엔티티에 여러 요소를 추가할 수 있고, 그중 어떤 요소를 갖고 어떤 요소를 갖지 않는지 조건을 지정해 엔티티를 조회할 수 있다면 ECS로 봅니다.

<a id="why-is-ecs-used"></a>

### ECS를 사용하는 이유는 무엇인가요?

게임 개발자 사이에서 ECS가 주목받는 이유는 여러 가지입니다.

- 대체로 더 많은 게임 오브젝트를 처리할 수 있습니다.
- 코드를 재사용하기 쉽습니다.
- 기존 코드에 새 기능을 추가하기 쉽습니다.
- 실행 중에 구성을 바꾸는 등 동적인 코드를 작성하기 좋습니다.

<a id="who-is-using-ecs"></a>

### 어디에서 ECS를 사용하고 있나요?

ECS는 여러 상용 프로젝트와 엔진에 쓰이고 있으며, 과거에 사용된 사례도 있습니다. 아래에 없는 사례를 알고 있다면 알려 주세요!

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

ECS는 객체 지향 프로그래밍(Object-Oriented Programming, OOP)의 대안으로 자주 소개됩니다. 두 방식에는 공통점도 있지만, 애플리케이션을 설계할 때 고려해야 할 차이도 있습니다.

- OOP는 상속을 중심으로, ECS는 조합을 중심으로 설계합니다.
- OOP는 데이터를 캡슐화하도록 권장합니다. ECS는 데이터를 외부에서 직접 다룰 수 있는 POD(plain old data) 객체를 권장합니다.
- OOP는 데이터와 동작을 함께 묶고, ECS는 둘을 분리합니다.
- OOP의 객체 인스턴스는 하나의 고정된 타입을 갖습니다. ECS의 엔티티는 여러 컴포넌트를 가질 수 있고, 그 구성도 실행 중에 바뀔 수 있습니다.

ECS도 *객체 지향 설계*의 특성을 갖추고 있으므로 객체 지향 설계의 한 종류로 봐야 한다는 주장도 있습니다. 관련 내용은 https://www.gamedev.net/blogs/entry/2265481-oop-is-dead-long-live-oop/ 를 참고하세요.

다만 실제로 ECS 애플리케이션을 설계하는 과정은 흔히 생각하는 OOP 방식과 상당히 다릅니다. 그런 점에서 ECS를 별도의 설계 방식으로 이해하고 접근하는 것이 도움이 됩니다.

<a id="how-is-ecs-different-from-entity-component-frameworks"></a>

### ECS와 엔티티-컴포넌트 프레임워크는 어떻게 다른가요?

이름이 비슷해 혼동하기 쉽지만, ECS와 엔티티-컴포넌트(Entity-Component, EC) 프레임워크는 다릅니다. 게임 엔진에서 흔히 볼 수 있는 EC 프레임워크도 엔티티를 만들고 컴포넌트를 조합한다는 점에서는 ECS와 비슷합니다. 차이는 컴포넌트에 있습니다. EC의 컴포넌트는 데이터와 동작을 함께 담는 클래스이며, 컴포넌트 자체에서 동작을 실행합니다.

간단한 EC 프레임워크를 코드로 나타내면 다음과 같습니다.

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

EC 프레임워크에서는 보통 `IComponent` 인터페이스를 상속해 기능을 구현하고, 이렇게 만든 컴포넌트들을 조합해 엔티티를 구성합니다. Unity의 `GameObject` 시스템이 대표적인 예입니다.

<a id="is-ecs-hard-to-learn"></a>

### ECS는 배우기 어려운가요?

ECS는 기본 개념과 규칙이 많지 않아 배우기 쉬운 편입니다. 다만 실제로 잘 활용하려면 연습이 필요할 수 있습니다. 특히 OOP에 익숙한 개발자라면 ECS의 설계 방식이 직관에 맞지 않게 느껴지는 부분도 있습니다.

사용자들의 경험담을 들어 보면, 어느 순간 ECS를 제대로 이해하고 나서부터는 코드를 작성하고 재사용하거나 규모를 키우기가 한결 수월해졌다고 합니다.

<a id="is-ecs-a-lower-level-of-abstraction"></a>

### ECS는 추상화 수준이 더 낮은가요?

꼭 그렇지는 않습니다. 일부 ECS는 하드웨어 수준의 최적화를 활용하도록 설계되어 있습니다. 그렇다고 ECS를 사용하는 코드 자체의 추상화 수준이 다른 방식보다 반드시 낮거나 높은 것은 아닙니다.

<a id="does-ecs-require-writing-more-code"></a>

### ECS를 사용하면 코드를 더 많이 작성해야 하나요?

일률적으로 답하기는 어렵습니다. 어떤 ECS 프레임워크와 엔진을 쓰느냐에 따라 크게 달라집니다.

ECS가 엔진에 통합되어 있다면 코드를 상당히 간결하게 작성할 수 있습니다. ECS를 쓰지 않을 때보다 코드가 짧아지기도 합니다. Bevy, Amethyst, Our Machinery가 이런 엔진의 예입니다.

반대로 ECS가 엔진에 통합되어 있지 않다면, 엔진에서 사용하는 타입과 ECS를 연결하는 코드가 추가로 필요해 전체 코드량이 늘어날 수 있습니다.

그래도 코드의 유지보수가 쉬워지므로, 처음 작성할 때 들인 시간은 이후 유지보수에서 절약하는 시간으로 만회할 수 있습니다.

<a id="is-ecs-good-for-low-level-code"></a>

### ECS는 저수준 코드에 적합한가요?

렌더링이나 물리 연산 같은 저수준 엔진 코드에서는 캐시 지역성을 높이고, 벡터화처럼 하드웨어가 제공하는 고급 기능을 활용해야 할 때가 있습니다. 이런 최적화에 얼마나 적합한지는 ECS 프레임워크마다 다릅니다.

일반적으로는 컴포넌트가 저장된 배열에 직접 접근할 수 있는 ECS가 저수준 최적화에 유리합니다. 특히 요즘 게임에서는 이런 시스템을 여러 스레드에서 실행하기가 얼마나 쉬운지도 중요한 선택 기준입니다.

<a id="can-ecs-be-implemented-in-any-language"></a>

### 어떤 언어로든 ECS를 구현할 수 있나요?

네.

<a id="should-i-write-my-own-ecs"></a>

### ECS를 직접 구현하는 게 좋을까요?

ECS는 개념과 규칙이 많지 않아서, 일단 동작하는 수준으로 만드는 일은 어렵지 않습니다. 직접 만들면 원하는 기능을 자유롭게 추가할 수 있고, 꼭 필요한 기능만 구현할 수 있다는 장점도 있습니다.

다만 직접 만든 ECS가 기존의 검증된 구현체보다 빠를 것이라고 기대해서는 안 됩니다. 기존 구현체에는 다양한 연산에서 고르게 좋은 성능을 내기 위해 오랫동안 개발해 온 기법들이 담겨 있습니다. 새로운 기법을 따라가려면 꾸준히 배우고 실험하며 구현을 개선해야 합니다.

다른 많은 일이 그렇듯 ECS도 만드는 법을 배우기는 쉽지만, 잘 만들기는 어렵습니다.

<a id="is-ecs-fast"></a>

### ECS는 빠른가요?

대체로 빠릅니다. 다만 무엇을 측정하는지, 어떤 구현체를 사용하는지에 따라 결과는 달라집니다. 구현체마다 성능을 위해 우선하는 부분이 다르므로, 어떤 프레임워크에서 매우 빠른 연산이 다른 프레임워크에서는 상당히 느릴 수도 있습니다.

ECS는 보통 조건에 맞는 엔티티들을 조회해 차례로 순회하거나, 실행 중에 컴포넌트 구성을 바꾸는 작업에 강합니다. 반면 이진 트리나 공간 자료 구조처럼 특수한 자료 구조가 필요한 조회나 연산에는 그다지 유리하지 않습니다. ECS의 성능을 최대한 끌어내려면 사용하는 구현체의 장단점을 이해하고, 그 설계에 맞게 활용해야 합니다.

<a id="is-ecs-code-more-reusable"></a>

### ECS 코드는 재사용하기 더 쉬운가요?

네. OOP에서는 동작이 특정 클래스에 묶이는 경우가 많지만, ECS에서는 엔티티가 어떤 컴포넌트를 가졌는지를 기준으로 동작을 적용하기 때문입니다. 이 차이는 재사용성에 몇 가지 영향을 줍니다.

우선 동작이 특정 클래스에 묶이지 않으므로, 서로 다른 종류의 엔티티에도 같은 동작을 적용할 수 있습니다. 예를 들어 `Move` 시스템은 `Position`과 `Velocity` 컴포넌트만 있으면 어떤 엔티티든 처리할 수 있습니다.

OOP 방식으로도 이런 설계가 가능하지만, 클래스 상속에 의존하는 경우가 많습니다. 상속을 사용하면 클래스 계층 구조를 리팩터링하기 어렵고, 공통 기반 클래스에 시간이 갈수록 불필요한 기능이 쌓이기 쉽다는 문제가 있습니다.

EC 프레임워크에서도 게임 엔티티에 컴포넌트를 추가하는 방식으로 비슷한 수준의 재사용성을 얻을 수 있습니다. 자세한 내용은 [ECS와 엔티티-컴포넌트 프레임워크는 어떻게 다른가요?](#how-is-ecs-different-from-entity-component-frameworks)를 참고하세요.

ECS의 큰 장점은 개발 도중 언제든 새 시스템을 추가할 수 있다는 것입니다. 필요한 컴포넌트만 갖추고 있으면 기존 엔티티든 새 엔티티든 자동으로 그 시스템의 처리 대상이 됩니다. 덕분에 시스템을 한 가지 역할만 맡는 작은 기능 단위로 만들기 쉽고, 다른 프로젝트에서도 쉽게 재사용할 수 있습니다.

<a id="is-ecs-good-for-multithreading"></a>

### ECS는 멀티스레딩에 적합한가요?

대체로 그렇습니다. 데이터와 동작이 분리되어 있어 각 시스템의 의존 관계를 파악하고 실행 순서와 시점을 정하기 쉽습니다. 멀티스레딩을 지원하는 방식은 구현체마다 다르지만, 대부분 여러 스레드에서 코드를 실행하기 쉽도록 돕습니다.

<a id="can-ecs-be-used-outside-of-gaming"></a>

### 게임 이외의 분야에서도 ECS를 사용할 수 있나요?

네. 게임 외의 프로젝트에도 활용할 수 있고, 실제로 그렇게 사용한 사례도 있습니다.

<a id="how-do-i-start-with-ecs"></a>

### ECS를 어떻게 시작하면 좋을까요?

ECS 관련 자료를 읽고, 거기서 소개하는 방식을 직접 실험해 보기를 적극 권합니다. 예제 프로젝트의 코드를 읽어 보는 것도 ECS로 애플리케이션을 만드는 방법을 빠르게 익히는 데 도움이 됩니다.

<a id="how-do-i-design-for-ecs"></a>

### ECS 기반 애플리케이션은 어떻게 설계하나요?

먼저 게임 데이터를 담을 자료 구조, 즉 컴포넌트부터 설계합니다. 이때 다음 사항을 고려해야 합니다.

- 데이터의 인스턴스가 얼마나 많이 필요한가?
- 데이터에 얼마나 자주 접근하는가?
- 데이터가 얼마나 자주 바뀌는가?
- 언제 데이터에 접근하거나 데이터를 변경해야 하는가?
- 함께 접근하거나 변경하는 데이터는 무엇인가?
- 데이터의 카디널리티(cardinality), 즉 서로 다른 값의 개수는 얼마인가?

각 컴포넌트와 시스템은 한 가지 역할만 맡도록 설계하는 것이 좋습니다. 그러면 다른 프로젝트에서 재사용하기도, 코드를 리팩터링하기도 쉬워집니다.

<a id="what-are-the-different-ways-to-implement-an-ecs"></a>

### ECS를 구현하는 방식에는 어떤 것들이 있나요?

ECS를 구현하는 방법은 다양하고, 각 방식마다 장단점이 있습니다. 여기서는 그중 널리 쓰이는 몇 가지를 소개합니다.

<a id="archetypes-aka-dense-ecs-or-table-based-ecs"></a>

#### 아키타입 방식(밀집 ECS 또는 테이블 기반 ECS)

아키타입(Archetype) 기반 ECS는 엔티티를 테이블에 저장합니다. 테이블의 각 열은 컴포넌트에, 각 행은 엔티티에 해당합니다. 이 방식은 조회와 순회가 빠릅니다.

아키타입 방식의 구현체로는 [Flecs](https://github.com/SanderMertens/flecs), [Our Machinery](https://ourmachinery.com/), [Unity DOTS](https://unity.com/dots), [Unreal Sequencer](https://www.unrealengine.com/en-US/tech-blog/performance-at-scale-sequencer-in-unreal-engine-4-26), [Unreal Mass](https://docs.unrealengine.com/5.0/en-US/overview-of-mass-entity-in-unreal-engine/), [Bevy ECS](https://bevyengine.org/), [Legion](https://github.com/amethyst/legion), [Hecs](https://github.com/Ralith/hecs), [Ark](https://github.com/mlange-42/ark)가 있습니다.

<a id="sparse-set-ecs-aka-sparse-ecs"></a>

#### 희소 집합 기반 ECS(희소 ECS)

희소 집합(sparse set) 기반 ECS는 컴포넌트 타입별로 희소 집합을 만들고, 엔티티 ID를 키로 삼아 컴포넌트를 저장합니다. 이 방식은 추가와 삭제가 빠릅니다.

희소 집합 방식의 구현체로는 [EnTT](https://github.com/skypjack/entt)와 [Shipyard](https://github.com/leudz/shipyard)가 있습니다.

<a id="bitset-based-ecs"></a>

#### 비트셋 기반 ECS

비트셋(bitset) 기반 ECS는 컴포넌트를 배열에 저장하고, 엔티티 ID를 배열의 인덱스로 사용합니다. 각 엔티티가 특정 컴포넌트를 갖고 있는지는 비트셋에 표시합니다.

구체적인 구현 방법은 여러 가지입니다. 컴포넌트 타입마다 배열과 비트셋을 하나씩 두고, 비트셋으로 해당 컴포넌트를 가진 엔티티를 표시할 수 있습니다. [hibitset](https://docs.rs/hibitset/0.6.3/hibitset/) 자료 구조를 사용하는 방법도 있습니다.

비트셋 방식의 구현체로는 [EntityX](https://github.com/alecthomas/entityx)와 [Specs](https://github.com/amethyst/specs)가 있습니다.

<a id="reactive-ecs"></a>

#### 반응형 ECS

반응형 ECS(reactive ECS)는 엔티티의 변경을 알리는 신호를 받아, 시스템이나 쿼리의 조건에 맞는 엔티티를 추적합니다.

이 방식을 사용하는 프레임워크로는 [Entitas](https://github.com/sschmid/Entitas-CSharp)가 있습니다.

<a id="how-are-components-modified"></a>

### 컴포넌트는 어떻게 수정하나요?

보통 두 가지 방법을 사용합니다. 엔티티 하나의 컴포넌트를 직접 수정하거나, 시스템에서 여러 엔티티의 컴포넌트 값을 수정합니다.

엔티티 하나의 컴포넌트를 수정하는 예입니다.

```cpp
entity.set<Position>({10, 20});
```

시스템에서 여러 엔티티의 컴포넌트를 수정하는 예입니다.

```cpp
system<Position, Velocity>().each(
    [](entity e, Position& p, Velocity & v) {
        p.x += v.x;
        p.y += v.y;
    });
```

일반적으로 두 번째 방식이 더 빠릅니다. 컴포넌트를 개별적으로 찾아오는 횟수가 적고, 컴포넌트가 효율적으로 배치되어 있다는 이점을 활용할 수 있기 때문입니다.

<a id="how-are-entities-matched-with-systems"></a>

### 시스템은 처리할 엔티티를 어떻게 찾나요?

주로 다음 세 가지 방식을 사용합니다.

1. 아키타입 기반 ECS에서는 쿼리가 조건에 맞는 테이블 목록을 저장합니다. 테이블 하나에 여러 엔티티가 들어갈 수 있습니다. 테이블 구성이 빠르게 안정되기 때문에, 조건에 맞는 테이블을 판별하는 데 드는 비용이 평균적으로 0까지 줄어든다는 장점이 있습니다.

2. 희소 집합 기반 ECS에서는 조회 조건에 포함된 컴포넌트 중 하나를 골라, 그 컴포넌트를 가진 엔티티를 모두 순회합니다. 보통은 해당 엔티티 수가 가장 적은 컴포넌트를 고릅니다. 그런 다음 각 엔티티가 나머지 컴포넌트도 갖고 있는지 확인합니다. 비트셋 기반 ECS도 비슷한 방식을 사용합니다.

3. 반응형 ECS에서는 시스템이 엔티티의 변경을 알리는 신호를 받습니다. 그 변경으로 엔티티가 조건에 맞게 되었는지 확인하고, 필요한 컴포넌트를 갖춘 엔티티를 모읍니다.

<a id="what-are-entity-relationships"></a>

### 엔티티 관계란 무엇인가요?

엔티티 관계(entity relationships)는 ECS 모델을 확장해, 컴포넌트 외에 두 요소를 묶은 쌍(pair)도 엔티티에 추가할 수 있게 한 기능입니다. 간단한 예를 보면 다음과 같습니다.

```c
alice.add<Likes>(bob);
```

여기서 `Likes`와 `bob`이 하나의 쌍을 이룹니다. `Likes`는 관계의 종류를, `bob`은 그 관계의 대상을 나타냅니다. `alice`와 `bob`은 모두 일반 엔티티입니다. 자세한 설명은 [이 글](https://ajmmertens.medium.com/building-games-in-ecs-with-entity-relationships-657275ba2c6c)을 참고하세요.

## 활용 방법

<a id="how-to-create-a-hierarchy-in-ecs"></a>

### ECS에서 계층 구조는 어떻게 만드나요?

계층 구조를 만드는 방법은 여러 가지이며, 사용할 수 있는 방법은 ECS 구현체에 따라 다릅니다. 어떤 구현체에서든 쓸 수 있는 방법은 다음처럼 컴포넌트에 계층 정보를 저장하는 것입니다.

```c
// 자식 엔티티의 컴포넌트에 부모 엔티티를 저장합니다
struct Parent {
    entity parent;
};

// 부모의 모든 자식 엔티티를 컴포넌트의 벡터에 저장합니다
struct Children {
    vector<entity> children;
};

// 자식 엔티티들을 연결 리스트로 관리합니다
struct ChildList {
    entity first_child; // 첫 번째 자식 엔티티
    entity prev_sibling; // 이전 형제 엔티티
    entity next_sibling; // 다음 형제 엔티티
};
```

이 방식은 유연하지만, 계층 구조를 순회할 때마다 컴포넌트를 찾아와야 하므로 시스템이 느려질 수 있습니다. 따라서 트랜스폼을 적용하는 것과 같은 저수준 시스템에는 그다지 적합하지 않습니다.

계층 구조를 위에서 아래로 순회하기만 하면 되는 경우에는, 계층의 깊이를 기준으로 엔티티를 정렬하는 방식이 잘 맞습니다. 빠르게 순회할 수 있지만 자주 정렬해야 한다는 단점이 있습니다.

아키타입 기반 ECS 중에는 하위 트리를 서로 다른 테이블에 나누어 저장할 수 있는 구현체도 있습니다. 이 경우 테이블이나 하위 트리를 깊이순으로 정렬할 수 있습니다. 순회가 빠르고 정렬도 자주 할 필요가 없지만, 작은 테이블이 많이 생기면 성능이 떨어질 수 있습니다.

<a id="how-to-store-spatial-data-in-ecs"></a>

### ECS에서 공간 데이터는 어떻게 저장하나요?

쿼드트리나 옥트리 같은 공간 자료 구조는 보통 ECS 안에 직접 저장하지 않습니다. 이런 자료 구조의 데이터 배치 방식이 일반적인 ECS의 저장 방식과 잘 맞지 않기 때문입니다.

ECS에서 정밀 단계(narrow phase)의 공간 쿼리를 처리할 때는, 관련 엔티티를 순회해 공간 자료 구조에 저장하는 쿼리를 만들어 두면 유용합니다. 이 쿼리를 매 프레임의 시작이나 끝에 실행하는 방식입니다.

광역 단계(broad phase)의 공간 쿼리에는 런타임 태그를 활용할 수 있습니다. 단, 사용하는 ECS가 이 기능을 지원해야 합니다. 공간 격자의 각 셀에 해당하는 태그를 두고 그 태그로 엔티티를 조회하면, 원하는 영역 밖에 있는 엔티티들을 한꺼번에 빠르게 제외할 수 있습니다.

## 데이터 지향 설계

<a id="what-is-data-oriented-design"></a>

### 데이터 지향 설계란 무엇인가요?

위키백과에서는 데이터 지향 설계(Data Oriented Design, DoD)를 다음과 같이 정의합니다.

> … CPU 캐시를 효율적으로 활용하기 위한 프로그램 최적화 방식으로, 비디오 게임 개발에 사용된다. 데이터 배치에 초점을 맞추고, 각 필드가 필요한 시점에 따라 필드를 분리하거나 정렬하며, 데이터를 어떻게 변환할지 중심으로 생각한다.

데이터 지향 설계는 여러 최적화 기법과 각 기법을 언제 적용해야 하는지를 아우르는 개념입니다. 핵심은 애플리케이션이 각 데이터를 어떤 패턴으로 사용하는지 분석한 뒤, _그 패턴에서_ 하드웨어를 가장 잘 활용할 수 있는 자료 구조를 선택하는 것입니다.

대표적인 최적화로는 CPU 캐시를 효율적으로 활용하기, 캐시 미스로 인해 RAM에서 데이터를 읽거나 쓰는 횟수 줄이기, SIMD 명령어 활용하기 등이 있습니다. 물론 이 밖에도 다양한 기법이 있습니다.

데이터 지향 설계를 폭넓게 알아보려면 Richard Fabian의 책 『Data Oriented Design』을 참고하세요.
[https://www.dataorienteddesign.com/dodbook/](https://www.dataorienteddesign.com/dodbook/)

<a id="is-ecs-the-same-as-dod"></a>

### ECS와 데이터 지향 설계는 같은 것인가요?

아니요. ECS를 사용하지 않아도 데이터 지향 설계 원칙에 따라 코드를 작성할 수 있습니다. 반대로 데이터 지향 설계를 활용하지 않는 ECS도 만들 수 있습니다.

다만 ECS 패턴은 데이터 지향 설계와 잘 맞습니다. 그래서 많은 ECS 프레임워크가 데이터 지향 설계의 최적화 기법을 활용할 수 있는 저장 구조를 제공합니다. 모든 프레임워크가 그런 것은 아닙니다.

컴포넌트를 메모리에 연속된 배열로 저장하고 그 배열을 순회하는 ECS라면, 데이터 지향 설계의 원칙과 최적화 기법을 활용할 수 있습니다.

<a id="what-is-random-access-memory"></a>

### RAM이란 무엇인가요?

RAM(Random Access Memory)은 컴퓨터에 보통 대용량으로 탑재되는 메모리입니다. 애플리케이션의 전체 상태와 코드가 여기에 저장되며, CPU는 코드를 실행하면서 RAM과 데이터를 주고받습니다.

RAM 자체는 매우 빠릅니다. 하지만 데이터를 많이 다루는 애플리케이션에서는 CPU와 RAM을 연결하는 버스가 병목이 될 수 있습니다. 그래서 데이터 지향 설계에서는 RAM에서 데이터를 읽어오는 횟수를 최대한 줄이려 합니다.

<a id="what-is-a-cpu-cache"></a>

### CPU 캐시란 무엇인가요?

CPU 캐시는 RAM보다 훨씬 빠른 대신 용량은 _훨씬_ 작은 메모리입니다. CPU가 RAM에서 읽어온 데이터는 캐시에 저장됩니다. 캐시는 여러 계층으로 나뉘며, 낮은 레벨의 캐시는 더 빠르고 용량이 큰 계층의 캐시는 더 느립니다.

데이터 지향 설계에서는 CPU 캐시를 최대한 활용해, RAM에서 데이터를 읽어와야 하는 횟수를 줄입니다.

<a id="what-is-a-cache-line"></a>

### 캐시 라인이란 무엇인가요?

캐시 라인(cache line)은 RAM에서 데이터를 한 번에 읽어오는 단위입니다. 예를 들어 애플리케이션이 4바이트만 요청하더라도, CPU는 요청한 주소를 포함하는 64바이트를 한꺼번에 읽어옵니다.

데이터를 메모리에서 서로 가까이 배치하면, 다음 연산에 필요한 데이터도 이미 캐시에 들어 있을 가능성이 높아집니다. 그만큼 RAM에서 데이터를 읽어오는 횟수를 줄일 수 있습니다.

<a id="what-is-locality-of-reference"></a>

### 참조 지역성이란 무엇인가요?

참조 지역성(locality of reference)은 시간적 지역성과 공간적 지역성으로 나뉩니다. 시간적 지역성은 짧은 시간 안에 같은 데이터를 다시 사용하는 것을 말합니다. 공간적 지역성은 데이터가 메모리에서 서로 가까운 위치에 저장되어 있는 것을 말합니다.

어느 쪽이든 지역성이 높으면 CPU가 데이터 접근 패턴을 더 잘 예측할 수 있어, 캐시를 효율적으로 활용할 수 있습니다.

<a id="what-is-simd"></a>

### SIMD란 무엇인가요?

SIMD는 Single Instruction Multiple Data의 약자입니다. 명령어 하나를 실행하는 시간에 여러 값을 함께 처리할 수 있는 명령어 또는 명령어 계열을 뜻합니다.

<a id="what-is-vectorization"></a>

### 벡터화란 무엇인가요?

벡터화(vectorization), 또는 자동 벡터화는 특정 조건을 만족하는 코드에 SIMD 명령어를 사용해 성능을 높이는 과정입니다. 이렇게 최적화한 코드는 일반 코드보다 몇 배 빠르게 실행될 수 있습니다.

벡터화를 위한 조건은 다음과 같습니다.

- 데이터가 배열처럼 메모리에 연속해서 저장되어 있어야 합니다.
- 코드에 분기나 함수 호출이 없어야 합니다.

컴파일러는 대체로 위 조건을 만족하는 루프를 벡터화할 수 있습니다. 다만 자동으로 벡터화할 수 있는 코드의 범위는 컴파일러마다 다릅니다. Clang이 지원하는 자동 벡터화 사례는 [이 문서](https://llvm.org/docs/Vectorizers.html)에서 확인할 수 있습니다.

<a id="what-is-false-sharing"></a>

### 거짓 공유란 무엇인가요?

거짓 공유(false sharing)는 서로 다른 스레드가 각기 다른 값을 읽고 수정하는데, 그 값들이 같은 캐시 라인에 있을 때 발생합니다. 이때 캐시를 동기화해야 하므로 성능이 떨어질 수 있습니다.

서로 다른 스레드가 사용하는 데이터를 같은 캐시 라인에 들어가지 않을 만큼 떨어뜨려 배치하면 거짓 공유를 피할 수 있습니다.

<a id="what-is-aos"></a>

### AoS란 무엇인가요?

AoS(Array of Structs, 구조체의 배열)는 여러 필드를 가진 구조체를 배열의 원소로 저장하는 메모리 배치 방식입니다. 다음과 같은 형태입니다.

```c
struct AoS {
  int m_1;
  int m_2;
};

AoS values[1000];
```

AoS는 데이터를 배열에 저장하므로 캐시 지역성에 유리합니다. 다만 구조체의 일부 멤버만 사용하는 코드에서도 필요하지 않은 데이터까지 캐시에 읽어올 수 있다는 단점이 있습니다.

ECS에서 AoS라고 하면 보통 모든 컴포넌트를 하나의 배열에 함께 저장하는 방식을 뜻합니다.

<a id="what-is-soa"></a>

### SoA란 무엇인가요?

SoA(Struct of Arrays, 배열의 구조체)는 각 필드를 별도의 배열로 만들고, 그 배열들을 하나의 구조체에 담는 메모리 배치 방식입니다. 다음과 같은 형태입니다.

```c
struct SoA {
  int m_1[1000];
  int m_2[1000];
};

SoA values;
```

AoS처럼 데이터를 배열에 저장하므로 캐시 지역성에 유리합니다. 구조체의 일부 멤버만 필요한 경우에는 다른 멤버를 캐시에 읽어오지 않아도 된다는 장점도 있습니다. 다만 다른 멤버에 불규칙하게 접근해야 하는 코드에서는 AoS보다 캐시 미스가 더 많이 발생할 수 있습니다.

ECS에서 SoA라고 하면 보통 컴포넌트별로 배열을 나누어 저장하는 방식을 뜻합니다.

<a id="what-is-branch-prediction"></a>

### 분기 예측이란 무엇인가요?

CPU는 명령어를 실행하면서 `if-else`나 `switch` 같은 조건문의 결과를 예측해, 다음에 어느 코드를 실행하게 될지 판단합니다. 그리고 예상한 경로의 명령어를 명령어 캐시에 미리 읽어오거나, 아예 미리 실행하기도 합니다.

예측하기 어려운 분기가 많으면 미리 계산한 결과를 버려야 하는 일이 잦아질 수 있습니다. 그만큼 코드의 실행 속도가 떨어집니다.

## 용어집

<a id="entity"></a>

### 엔티티(Entity)

엔티티는 게임 속 개별 대상을 나타냅니다. ECS에서는 보통 엔티티를 고유한 정숫값으로 표현합니다.

<a id="component"></a>

### 컴포넌트(Component)

컴포넌트는 엔티티에 추가하거나 제거할 수 있는 데이터 타입입니다. ECS에서는 보통 데이터를 캡슐화하지 않고 외부에서 직접 접근할 수 있는 단순한 타입을 사용합니다.

<a id="tag"></a>

### 태그(Tag)

태그는 데이터가 없는 컴포넌트입니다.

<a id="system"></a>

### 시스템(System)

시스템은 특정 컴포넌트들을 가진 모든 엔티티를 처리 대상으로 삼는 실행 가능한 객체입니다.

<a id="query"></a>

### 쿼리(Query)

쿼리는 시스템과 비슷하지만, 쿼리 자체를 실행할 수는 없습니다.

<a id="world"></a>

### 월드(World)

월드는 ECS의 모든 데이터를 담는 컨테이너입니다. 많은 ECS 프레임워크가 하나의 애플리케이션에서 여러 월드를 사용할 수 있도록 지원합니다.

<a id="registry"></a>

### 레지스트리(Registry)

월드와 같은 의미입니다.

<a id="archetype"></a>

### 아키타입(Archetype)

특정 컴포넌트 조합을 가진 엔티티들을 저장하는 자료 구조입니다. 각 컴포넌트는 테이블의 열에 해당하며, 메모리에 연속된 배열로 저장됩니다.

<a id="table"></a>

### 테이블(Table)

테이블은 흔히 아키타입과 같은 뜻으로 쓰입니다. 다만 일부 ECS 구현체는 두 용어를 구분합니다.

이런 구현체에서 테이블은 ‘밀집’ 컴포넌트를 저장하는 아키타입을 뜻합니다. 밀집 컴포넌트는 연속된 배열에 저장되며, 배열마다 같은 인덱스가 같은 엔티티를 가리킵니다. 반면 아키타입에는 ‘희소’ 컴포넌트와 테이블을 참조하는 인덱스를 저장합니다. 희소 컴포넌트는 메모리에 연속해서 저장되지 않거나, 배열 간에 인덱스가 서로 대응하지 않는 컴포넌트를 말합니다.

<a id="sparse-set"></a>

### 희소 집합(Sparse set)

순회, 조회, 삽입, 삭제를 빠르게 수행할 수 있는 자료 구조입니다. 해시 맵과 비슷하지만, 순차적으로 부여된 식별자를 다루는 데 더 적합합니다.
