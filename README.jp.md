# GASDocumentation
アンリアル・エンジン4のGameplayAbilitySystemプラグイン(GAS)について、簡単なマルチプレイヤーのサンプルプロジェクトを使って理解します。これは公式のドキュメントではなく、このプロジェクトも私もEpic Gamesとは関係ありません。私はこの情報の正確さを保証するものではありません。

このドキュメントの目的は、GASの主要なコンセプトとクラスを説明し、私の経験に基づいていくつかの追加コメントを提供することです。コミュニティ内のユーザーの間では、GASに関する多くの「部族的知識」がありますが、私はここですべてを共有することを目指しています。

このサンプル プロジェクトとドキュメントは、**Unreal Engine 4.26** に対応しています。古いバージョンの Unreal Engine 用のドキュメントもありますが、これらはサポートが終了しており、バグや古い情報が含まれている可能性があります。

[GASShooter](https://github.com/dohi/GASShooter)は、マルチプレイヤーのFPS/TPSでGASを使った高度なテクニックを実演する姉妹サンプルプロジェクトです。

最良のドキュメントは、常にプラグインのソースコードです。

<a name="table-of-contents"></a>
## Table of Contents

> 1. [GameplayAbilitySystemプラグインの概要](#intro)
> 1. [サンプルプロジェクト](#sp)
> 1. [GASを使用したプロジェクトのセットアップ](#setup)
> 1. [コンセプト](#concepts)  
>    4.1 [Ability System Component](#concepts-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.1 [Replication Mode](#concepts-asc-rm)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.2 [セットアップと初期化](#concepts-asc-setup)  
>    4.2 [Gameplay Tags](#concepts-gt)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.1 [ゲームプレイタグの変更に対応する](#concepts-gt-change)  
>    4.3 [Attributes](#concepts-a)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.1 [Attribute 定義](#concepts-a-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.2 [BaseValue vs CurrentValue](#concepts-a-value)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.3 [Meta Attributes](#concepts-a-meta)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.4 [Attributeの変更に対応する](#concepts-a-changes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.5 [Derived Attributes](#concepts-a-derived)  
>    4.4 [Attribute Set](#concepts-as)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.1 [Attribute Set 定義](#concepts-as-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2 [Attribute Set 設計](#concepts-as-design)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.1 [個々のAttributeを持つサブコンポーネント](#concepts-as-design-subcomponents)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.2 [AttributeSetsの実行時の追加および削除](#concepts-as-design-addremoveruntime)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3 [アイテムAttribute（武器の弾倉）](#concepts-as-design-itemattributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.1 [Plain Floats on the Item](#concepts-as-design-itemattributes-plainfloats)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.2 [`AttributeSet` on the Item](#concepts-as-design-itemattributes-attributeset)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.3 [`ASC` on the Item](#concepts-as-design-itemattributes-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.3 [Attributesを定義する](#concepts-as-attributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.4 [Attributesを初期化する](#concepts-as-init)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.5 [PreAttributeChange()](#concepts-as-preattributechange)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.6 [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.7 [OnAttributeAggregatorCreated()](#concepts-as-onattributeaggregatorcreated)  
>    4.5 [Gameplay Effects](#concepts-ge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.1 [Gameplay Effect 定義](#concepts-ge-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.2 [Gameplay Effectsの適用](#concepts-ge-applying)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.3 [Gameplay Effectsの削除](#concepts-ga-removing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4 [Gameplay Effect Modifiers](#concepts-ge-mods)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.1 [Multiply and Divide Modifiers](#concepts-ge-mods-multiplydivide)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.2 [Gameplay Tags on Modifiers](#concepts-ge-mods-gameplaytags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.5 [Stacking Gameplay Effects](#concepts-ge-stacking)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.6 [Granted Abilities](#concepts-ge-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.7 [Gameplay Effect Tags](#concepts-ge-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.8 [Immunity](#concepts-ge-immunity)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9 [Gameplay Effect Spec](#concepts-ge-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9.1 [SetByCallers](#concepts-ge-spec-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.10 [Gameplay Effect Context](#concepts-ge-context)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.11 [Modifier Magnitude Calculation](#concepts-ge-mmc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12 [Gameplay Effect Execution Calculation](#concepts-ge-ec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1 [Sending Data to Execution Calculations](#concepts-ge-ec-senddata)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.1 [SetByCaller](#concepts-ge-ec-senddata-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.2 [Backing Data Attribute Calculation Modifier](#concepts-ge-ec-senddata-backingdataattribute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.3 [Backing Data Temporary Variable Calculation Modifier](#concepts-ge-ec-senddata-backingdatatempvariable)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.4 [Gameplay Effect Context](#concepts-ge-ec-senddata-effectcontext)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.13 [Custom Application Requirement](#concepts-ge-car)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.14 [Cost Gameplay Effect](#concepts-ge-cost)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15 [Cooldown Gameplay Effect](#concepts-ge-cooldown)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.1 [Get the Cooldown Gameplay Effect's Remaining Time](#concepts-ge-cooldown-tr)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.2 [Listening for Cooldown Begin and End](#concepts-ge-cooldown-listen)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.3 [Predicting Cooldowns](#concepts-ge-cooldown-prediction)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.16 [Changing Active Gameplay Effect Duration](#concepts-ge-duration)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.17 [Creating Dynamic Gameplay Effects at Runtime](#concepts-ge-dynamic)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.18 [Gameplay Effect Containers](#concepts-ge-containers)  
>    4.6 [Gameplay Abilities](#concepts-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1 [Gameplay Ability Definition](#concepts-ga-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.1 [Replication Policy](#concepts-ga-definition-reppolicy)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.2 [Server Respects Remote Ability Cancellation](#concepts-ga-definition-remotecancel)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.3 [Replicate Input Directly](#concepts-ga-definition-repinputdirectly)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2 [Binding Input to the ASC](#concepts-ga-input)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2.1 [Binding to Input without Activating Abilities](#concepts-ga-input-noactivate)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.3 [Granting Abilities](#concepts-ga-granting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4 [Activating Abilities](#concepts-ga-activating)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.1 [Passive Abilities](#concepts-ga-activating-passive)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.5 [Canceling Abilities](#concepts-ga-cancelabilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.6 [Getting Active Abilities](#concepts-ga-definition-activeability)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.7 [Instancing Policy](#concepts-ga-instancing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.8 [Net Execution Policy](#concepts-ga-net)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.9 [Ability Tags](#concepts-ga-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.10 [Gameplay Ability Spec](#concepts-ga-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.11 [Passing Data to Abilities](#concepts-ga-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.12 [Ability Cost and Cooldown](#concepts-ga-commit)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.13 [Leveling Up Abilities](#concepts-ga-leveling)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.14 [Ability Sets](#concepts-ga-sets)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.15 [Ability Batching](#concepts-ga-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.16 [Net Security Policy](#concepts-ga-netsecuritypolicy)  
>    4.7 [Ability Tasks](#concepts-at)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.1 [Ability Task Definition](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.2 [Custom Ability Tasks](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.3 [Using Ability Tasks](#concepts-at-using)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.4 [Root Motion Source Ability Tasks](#concepts-at-rms)  
>    4.8 [Gameplay Cues](#concepts-gc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.1 [Gameplay Cue Definition](#concepts-gc-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.2 [Triggering Gameplay Cues](#concepts-gc-trigger)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.3 [Local Gameplay Cues](#concepts-gc-local)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.4 [Gameplay Cue Parameters](#concepts-gc-parameters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.5 [Gameplay Cue Manager](#concepts-gc-manager)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.6 [Prevent Gameplay Cues from Firing](#concepts-gc-prevention)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7 [Gameplay Cue Batching](#concepts-gc-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.1 [Manual RPC](#concepts-gc-batching-manualrpc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.2 [Multiple GCs on one GE](#concepts-gc-batching-gcsonge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.8 [Gameplay Cue Events](#concepts-gc-events)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.9 [Gameplay Cue Reliability](#concepts-gc-reliability)  
>    4.9 [Ability System Globals](#concepts-asg)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.9.1 [InitGlobalData()](#concepts-asg-initglobaldata)  
>    4.10 [Prediction](#concepts-p)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.1 [Prediction Key](#concepts-p-key)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.2 [Creating New Prediction Windows in Abilities](#concepts-p-windows)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.3 [Predictively Spawning Actors](#concepts-p-spawn)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.4 [Future of Prediction in GAS](#concepts-p-future)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.5 [Network Prediction Plugin](#concepts-p-npp)  
>    4.11 [Targeting](#concepts-targeting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.1 [Target Data](#concepts-targeting-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.2 [Target Actors](#concepts-targeting-actors)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.3 [Target Data Filters](#concepts-target-data-filters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.4 [Gameplay Ability World Reticles](#concepts-targeting-reticles)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.5 [Gameplay Effect Containers Targeting](#concepts-targeting-containers)  
> 1. [Commonly Implemented Abilities and Effects](#cae)  
>    5.1 [Stun](#cae-stun)  
>    5.2 [Sprint](#cae-sprint)  
>    5.3 [Aim Down Sights](#cae-ads)  
>    5.4 [Lifesteal](#cae-ls)  
>    5.5 [Generating a Random Number on Client and Server](#cae-random)  
>    5.6 [Critical Hits](#cae-crit)  
>    5.7 [Non-Stacking Gameplay Effects but Only the Greatest Magnitude Actually Affects the Target](#cae-nonstackingge)  
>    5.8 [Generate Target Data While Game is Paused](#cae-paused)  
>    5.9 [One Button Interaction System](#cae-onebuttoninteractionsystem)  
> 1. [Debugging GAS](#debugging)  
>    6.1 [showdebug abilitysystem](#debugging-sd)  
>    6.2 [Gameplay Debugger](#debugging-gd)  
>    6.3 [GAS Logging](#debugging-log)  
> 1. [Optimizations](#optimizations)  
>    7.1 [Ability Batching](#optimizations-abilitybatching)  
>    7.2 [Gameplay Cue Batching](#optimizations-gameplaycuebatching)  
>    7.3 [AbilitySystemComponent Replication Mode](#optimizations-ascreplicationmode)  
>    7.4 [Attribute Proxy Replication](#optimizations-attributeproxyreplication)  
>    7.5 [ASC Lazy Loading](#optimizations-asclazyloading)  
> 1. [Quality of Life Suggestions](#qol)  
>    8.1 [Gameplay Effect Containers](#qol-gameplayeffectcontainers)  
>    8.2 [Blueprint AsyncTasks to Bind to ASC Delegates](#qol-asynctasksascdelegates)  
> 1. [Troubleshooting](#troubleshooting)  
>    9.1 [`LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`](#troubleshooting-notlocal)  
>    9.2 [`ScriptStructCache` errors](#troubleshooting-scriptstructcache)  
>    9.3 [Animation Montages are not replicating to clients](#troubleshooting-replicatinganimmontages)  
>    9.4 [Duplicating Blueprint Actors is setting AttributeSets to nullptr](#troubleshooting-duplicatingblueprintactors)  
> 1. [Common GAS Acronyms](#acronyms)
> 1. [Other Resources](#resources)  
>    11.1 [Q&A With Epic Game's Dave Ratti](#resources-daveratti)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.1 [Community Questions 1](#resources-daveratti-community1)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.2 [Community Questions 2](#resources-daveratti-community2)  
> 1. [GAS Changelog](#changelog)  
>    * [4.26](#changelog-4.26)  
>    * [4.25.1](#changelog-4.25.1)  
>    * [4.25](#changelog-4.25)  
>    * [4.24](#changelog-4.24)
         
<a name="intro"></a>
## 1. GameplayAbilitySystem プラグインの概要
公式ドキュメント](https://docs.unrealengine.com/4.26/ja/InteractiveExperiences/GameplayAbilitySystem/)より。
>ゲームプレイアビリティシステム」は、RPGやMOBAのようなタイプのアビリティや属性を構築するための、柔軟性の高いフレームワークです。ゲーム内のキャラクターが使用するアクションやパッシブアビリティ、アクションの結果として様々な属性を強化したり消耗したりするステータス効果、アクションの使用を規制する「クールダウン」タイマーやリソースコストの実装、アビリティのレベルやレベルごとの効果の変更、パーティクルやサウンドエフェクトの起動など、様々なことが可能です。簡単に言えば、このシステムは、現代のRPGやMOBAタイトルにおいて、ジャンプのような単純なものから、お気に入りのキャラクターのアビリティセットのような複雑なものまで、ゲーム内のアビリティをデザインし、実装し、効率的にネットワーク化するのに役立ちます。

GameplayAbilitySystemプラグインは、Epic Gamesによって開発され、Unreal Engine 4 (UE4)に付属しています。このプラグインは、ParagonやFortniteなどのAAA級商用ゲームでバトルテストが行われています。

このプラグインは、シングルおよびマルチプレイヤーゲームにおいて、以下のようなすぐに使えるソリューションを提供します。
* レベルベースのキャラクターのアビリティやスキルを実装し、オプションでコストやクールダウンを設定する ([GameplayAbilities](#concepts-ga))。
* アクターの数値的な`属性`の操作 ([Attributes](#concepts-a))
* アクターへのステータス効果の適用 ([GameplayEffects](#concepts-ge))
* `GameplayTags`をアクターに適用する ([GameplayTags](#concepts-gt))
* ビジュアルやサウンドエフェクトの生成 ([GameplayCues](#concepts-gc))
* 前述のすべてのもののレプリケーション

マルチプレイヤーゲームでは、GASは以下の[クライアントサイドの予測](#concepts-p)をサポートしています。
* アビリティの起動
* アニメーションモンタージュの再生
* `Attributes` への変更
* `GameplayTags` の適用
* GameplayCues`の生成
* `CharacterMovementComponent`に接続された`RootMotionSource`関数による移動。

**GASはC++で設定する必要がありますが**、`GameplayAbilities`や`GameplayEffects`はデザイナーがブループリントで作成することができます。

GASの現在の問題点
* `GameplayEffects`のレイテンシーの調整（アビリティのクールダウンを予測できないため、レイテンシーの高いプレイヤーは、レイテンシーの低いプレイヤーに比べて、クールダウンの低いアビリティの発射速度が低くなる）。
* `GameplayEffects` の削除を予測することはできません。しかし、逆の効果を持つ`GameplayEffects`を追加して、効果的に取り除くことは予測できます。これは常に適切であるとは限らず、また実現可能でもないため、依然として問題が残っています。
* ボイラープレート・テンプレート、マルチプレイヤーの例、ドキュメントが不足しています。これがその助けになれば幸いです。

**[⬆ Back to Top](#table-of-contents)**

<a name="sp"></a>
## 2. サンプルプロジェクト
このドキュメントには、GameplayAbilitySystemプラグインを初めて使用するが、アンリアル・エンジン4を初めて使用するわけではない人を対象とした、マルチプレイヤーの3人称シューティングゲームのサンプルプロジェクトが含まれています。C++、ブループリント、UMG、レプリケーションなど、UE4の中級者向けの知識をお持ちの方を想定しています。このプロジェクトでは、基本的なサードパーソンシューターのマルチプレイヤー対応プロジェクトに、プレイヤーやAIが操作するヒーロー用の`PlayerState`クラスに`AbilitySystemComponent`（`ASC`）を、AIが操作するミニオン用の`Character`クラスに`ASC`を設定する例を紹介します。

目標は、このプロジェクトをシンプルに保ちつつ、GASの基本を示し、よく求められる能力をコメント付きのコードで実演することです。初心者向けということもあり、このプロジェクトでは[発射物の予測](#concepts-p-spawn)のような高度なトピックは紹介していません。

概念の説明
* `ASC`を`PlayerState` と `Character` のどちらに置くか
* レプリケートされた `Attributes` (属性)
* レプリケートされたアニメーション・モンタージュ
* `GameplayTags`(ゲームプレイタグ)
* `GameplayEffects` を `GameplayAbilities` の内部および外部に適用、削除する。
* キャラクターのヘルスを変更するために、アーマーによって軽減されるダメージを適用する。
* `GameplayEffectExecutionCalculations`(ゲームプレイエフェクトの計算)
* スタン効果
* 死と リスポーン
* アビリティからアクター(投射物)をサーバー上にスポーンする
* エイムダウンサイトやスプリントでローカルプレイヤーの速度を予測的に変化させる
* 疾走するために常にスタミナを消耗する
* アビリティの発動にマナを使う
* パッシブアビリティ
* `GameplayEffects` (ゲームプレイ効果) のスタッキング
* アクタのターゲッティング
* ブループリントで作成された`GameplayAbilities`
* C++で作成された`GameplayAbilities`（ゲームプレイアビリティ）
* インスタンス化された `Actor` あたりの `GameplayAbilities`
* インスタンス化されていない `GameplayAbilities` (ジャンプ)
* スタティックな`GameplayCues`(FireGunの投射物のインパクトパーティクルエフェクト)
* アクター `GameplayCues` (スプリントとスタンのパーティクルエフェクト)

ヒーロークラスは以下のようなアビリティを持っています。

| アビリティ                 | 入力          | 予測の有無  | C++ / Blueprint | 説明                                                                                                                                                                  |
| -------------------------- | ------------------- | ---------- | --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Jump                       | Space Bar           | Yes        | C++             | ヒーロをジャンプさせる。
| Gun                        | Left Mouse Button   | No         | C++             | ヒーローの銃から弾を発射します。アニメーションは予測されますが、発射物は予測されません。
| Aim Down Sights            | Right Mouse Button  | Yes        | Blueprint       | ボタンを押している間は、主人公の歩く速度が遅くなり、カメラがズームアップして銃をより正確に撃てるようになります。
| Sprint                     | Left Shift          | Yes        | Blueprint       | ボタンを押している間、ヒーローはより速く走り、スタミナを消耗します。
| Forward Dash               | Q                   | Yes        | Blueprint       | 主人公はスタミナを消費してダッシュで前進します。
| Passive Armor Stacks       | Passive             | No         | Blueprint       | 4秒ごとにヒーローは最大4スタックまでのアーマーを獲得する。ダメージを受けると1スタック分のアーマーがなくなる。
| Meteor                     | R                   | No         | Blueprint       | プレイヤーがターゲットした場所に流星を投下し、敵にダメージを与え、スタンさせます。ターゲットは予測されますが、流星の投下は予測されません。

`GameplayAbilities`は、C++で作成しても、ブループリントで作成しても問題ありません。ここでは、それぞれの言語での作成方法の例として、2つの言語を混合して使用しています。

ミニオンには、事前に定義された`GameplayAbilities`はありません。赤のミニオンはヘルスの回復量が多く、青のミニオンはスタート時のヘルスが高い。

`GameplayAbility`の命名には、ブループリントで作成された`GameplayAbility`のロジックを示すために、サフィックス「_BP」を使用しました。サフィックスがない場合は、ロジックがC++で作成されていることを意味します。

**Blueprint アセット命名接頭辞**

| Prefix      | Asset Type          |
| ----------- | ------------------- |
| GA_         | GameplayAbility     |
| GC_         | GameplayCue         |
| GE_         | GameplayEffect      |

**[⬆ Back to Top](#table-of-contents)**

<a name="setup"></a>
## 3. GASを使ったプロジェクトの立ち上げ
GASを使ってプロジェクトを立ち上げる基本的な手順をご紹介します。
1. エディタでGameplayAbilitySystemプラグインを有効にする。
1. `YourProjectName.Build.cs`を編集し、`PrivateDependencyModuleNames`に`"GameplayAbilities", "GamplayTags", "GamplayTasks"`を追加する。
1. Visual Studioのプロジェクトファイルを更新・再生成します。
1. 4.24以降、[`TargetData`](#concepts-targeting-data)を使用するためには、`UAbilitySystemGlobals::InitGlobalData()`を呼び出すことが必須となりました。サンプルプロジェクトでは、`UEngineSubsystem::Initialize()`でこれを行っています。詳しくは、[`InitGlobalData()`](#concepts-asg-initglobaldata)を参照してください。

以上でGASを有効にすることができます。ここからは、自分の`Character`や`PlayerState`に[`ASC`](#concepts-asc)や[`AttributeSet`](#concepts-as)を追加して、[`GameplayAbilities`](#concepts-ga)や[`GameplayEffects`](#concepts-ge)を作ることができます。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts"></a>
## 4. GAS のコンセプト

#### Sections

> 4.1 [Ability System Component](#concepts-asc)  
> 4.2 [Gameplay Tags](#concepts-gt)  
> 4.3 [Attributes](#concepts-a)  
> 4.4 [Attribute Set](#concepts-as)  
> 4.5 [Gameplay Effects](#concepts-ge)  
> 4.6 [Gameplay Abilities](#concepts-ga)  
> 4.7 [Ability Tasks](#concepts-at)  
> 4.8 [Gameplay Cues](#concepts-gc)  
> 4.9 [Ability System Globals](#concepts-asg)  
> 4.10 [Prediction](#concepts-p)

<a name="concepts-asc"></a>
### 4.1 アビリティシステムコンポーネント
`AbilitySystemComponent`(`ASC`)はGASの心臓部であり、システムとのすべてのインタラクションを処理する`UActorComponent`([`UAbilitySystemComponent`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemComponent/index.html))です。[`GameplayAbilities`](#concepts-ga)を使用したい、[`Attributes`](#concepts-a)を持ちたい、[`GameplayEffects`](#concepts-ge)を受け取りたいと思っている`Actor`は、必ず`ASC`を装着する必要があります。これらのオブジェクトはすべて`ASC`の内部に存在し、[AttributeSet](#concepts-as)によって複製される`Attributes`を除いて、`ASC`によって管理され複製されます。開発者はこれをサブクラス化することが期待されますが、必須ではありません。

この `ASC` がアタッチされた `Actor` は `ASC` の `OwnerActor` と呼ばれます。また、`ASC` の物理的な表現である `Actor` を `AvatarActor` と呼びます。`OwnerActor`と`AvatarActor`は、MOBAゲームの単純なAIミニオンのように、同じ`Actor`にすることができます。また、MOBAゲームでプレイヤーが操作するヒーローの場合、`OwnerActor`が`PlayerState`で、`AvatarActor`がヒーローの`Character`クラスのように、異なる`Actor`になることもあります。ほとんどの `Actor` は自分自身に `ASC` を持ちます。もし、`Actor`がリスポーンし、スポーン間で`Attributes`や`GameplayEffects`の持続性が必要な場合（MOBAのヒーローのように）、`ASC`の理想的な位置は`PlayerState`となります。

**注意：** もし `ASC` が `PlayerState` 上にある場合は、`PlayerState` の `NetUpdateFrequency` を上げる必要があります。デフォルトでは、`PlayerState`の値は非常に低く、`Attributes`や`GameplayTags`などの変更がクライアント上で行われる前に、遅延が発生したり、ラグを感じることがあります。[`Adaptive Network Update Frequency`](https://docs.unrealengine.com/en-US/Gameplay/Networking/Actors/Properties/index.html#adaptivenetworkupdatefrequency)を必ず有効にしてください。Fortniteではそうしています。

`OwnerActor`と`AvatarActor`が異なる`Actor`の場合は、`IAbilitySystemInterface`を実装する必要があります。このインターフェースには、オーバーライドしなければならない関数として、`UAbilitySystemComponent* GetAbilitySystemComponent() const`があり、これはその`ASC`へのポインタを返します。`ASC` はこのインタフェース関数を探してシステム内部で相互に作用します。

`ASC`は現在のアクティブな`GameplayEffects`を`FActiveGameplayEffectsContainer ActiveGameplayEffects`に保持します。

また、`ASC`は付与された`GameplayAbility`を`FGameplayAbilitySpecContainer ActivatableAbilities`に保持します。`ActivatableAbilities.Items` を繰り返し処理するときは、必ずループの上に `ABILITYLIST_SCOPE_LOCK();` を追加して、(アビリティを削除したときに)リストが変化しないようにロックしてください。スコープ内のすべての `ABILITYLIST_SCOPE_LOCK();` は `AbilityScopeLockCount` をインクリメントし、スコープ外に出るとデクリメントします。`ABILITYLIST_SCOPE_LOCK();`のスコープ内でアビリティを削除しようとしないでください（アビリティクリア関数はリストがロックされている場合にアビリティを削除しないように内部で`AbilityScopeLockCount`をチェックしています）。

<a name="concepts-asc-rm"></a>
### 4.1.1 レプリケーションモード
`ASC`では`GameplayEffects`、`GameplayTags`、`GameplayCues`を複製するための3つの異なるレプリケーションモード、`Full`、`Mixed`、`Minimal`を定義しています。`Attribute` はその `AttributeSet` によって複製されます。

| レプリケーションモード   | いつ利用するか                             | 説明                                                                                                                    |
| ------------------ | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `Full`             | シングルプレイヤー                           | すべての `GameplayEffect` は、すべてのクライアントに複製されます。
| `Mixed`            | マルチプレイヤー、プレイヤーが操作する `Actors` | `GameplayEffects`は所有するクライアントにのみレプリケートされます。`GameplayTags`と`GameplayCues`だけが全員にレプリケートされます。
| `Minimal`          | マルチプレイヤー、AIが操作する `Actors`     | `GameplayEffects`は誰にも複製されません。`GameplayTags`と`GameplayCues`だけが全員にレプリケートされます。|

**注意：** `Mixed` レプリケーションモードでは、`OwnerActor` の `Owner` が `Controller` になることを想定しています。PlayerStateの`Owner`はデフォルトで`Controller`になりますが、`Character's`はなりません。もし`Mixed`レプリケーションモードで`PlayerState`ではなく`OwnerActor`を使用する場合には、有効な`Controller`を持つ`OwnerActor`に対して`SetOwner()`をコールする必要があります。

4.24 以降、`PossessedBy()` は、`Pawn` のオーナーを新しい `Controller` に設定します。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-asc-setup"></a>
### 4.1.2 セットアップと初期化
`ASC`は通常、`OwnerActor`のコンストラクタで構築され、明示的にレプリケートとマークします。**これはC++で行う必要があります**。

```c++
AGDPlayerState::AGDPlayerState()
{
	// Create ability system component, and set it to be explicitly replicated
	AbilitySystemComponent = CreateDefaultSubobject<UGDAbilitySystemComponent>(TEXT("AbilitySystemComponent"));
	AbilitySystemComponent->SetIsReplicated(true);
	//...
}
```

サーバーとクライアントの両方で `ASC` を `OwnerActor` と `AvatarActor` で初期化する必要があります。初期化は、`Pawn`の`Controller` が設定された後（所有した後）に行う必要があります。シングルプレイヤーゲームでは、サーバーのパスだけを気にすればよいでしょう。

プレイヤーが操作するキャラクターで、`ポーン`に`ASC`が設定されている場合、私は通常、サーバーでは`ポーン`の`PossessedBy()`関数で初期化し、クライアントでは`PlayerController`の`AcknowledgePossession()`関数で初期化します。

```c++
void APACharacterBase::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->InitAbilityActorInfo(this, this);
	}

	// ASC MixedModeのレプリケーションでは、ASCのオーナー（Pawn）のオーナーが、コントローラーとなることが必要です。
	SetOwner(NewController);
}
```

```c++
void APAPlayerControllerBase::AcknowledgePossession(APawn* P)
{
	Super::AcknowledgePossession(P);

	APACharacterBase* CharacterBase = Cast<APACharacterBase>(P);
	if (CharacterBase)
	{
		CharacterBase->GetAbilitySystemComponent()->InitAbilityActorInfo(CharacterBase, CharacterBase);
	}

	//...
}
```

プレイヤーが操作するキャラクターで、`ASC`が`PlayerState`上に存在する場合、私は通常、`Pawn`の`PossessedBy()`関数でサーバーを初期化し、`Pawn`の`OnRep_PlayerState()`関数でクライアントを初期化します。これにより、クライアント上に `PlayerState` が存在することが保証されます。

```c++
// Server only
void AGDHeroCharacter::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// Set the ASC on the Server. Clients do this in OnRep_PlayerState()
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// AIはPlayerControllersを持っていないので、念のためここでもう一度initします。PlayerControllerを持つヒーローが2回initしても害はありません。
		PS->GetAbilitySystemComponent()->InitAbilityActorInfo(PS, this);
	}
	
	//...
}
```

```c++
// Client only
void AGDHeroCharacter::OnRep_PlayerState()
{
	Super::OnRep_PlayerState();

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// Set the ASC for clients. Server does this in PossessedBy.
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// Init ASC Actor Info for clients. Server will init its ASC when it possesses a new Actor.
		AbilitySystemComponent->InitAbilityActorInfo(PS, this);
	}

	// ...
}
```

`LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!` というエラーメッセージが表示されたら、クライアントの `ASC` を初期化していません。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gt"></a>
### 4.2 ゲームプレイタグ
[`FGameplayTags`](https://docs.unrealengine.com/en-US/API/Runtime/GameplayTags/FGameplayTag/index.html)は、`Parent.Child.Grandchild...`という形式の階層的な名前で、`GameplayTagManager`に登録されます。これらのタグは、オブジェクトの状態を分類して記述するのに非常に役立ちます。例えば、キャラクターがスタンしている場合、スタンしている間、`State.Debuff.Stun` `GameplayTag`を与えることができる。

これまでBooleanやEnumで処理していたものを`GameplayTags`に置き換えて、オブジェクトが特定の`GameplayTags`を持っているかどうかのブーリアンロジックを行うことになります。

オブジェクトにタグを付与する場合、GASがタグを操作できるように、ASCがあればそれに追加するのが一般的です。`UAbilitySystemComponent` は `IGameplayTagAssetInterface` を実装しており、所有する `GameplayTags` にアクセスするための関数を提供します。

複数の `GameplayTags` を `FGameplayTagContainer` に格納することができます。なぜなら、`GameplayTagContainer`はいくつかの効率的なマジックを追加するからです。タグは標準的な`FNames`ですが、プロジェクトの設定で`Fast Replication`が有効になっていれば、レプリケーションのために`FGameplayTagContainers`に効率的にまとめることができます。`Fast Replication`では、サーバーとクライアントが同じ`GameplayTags`のリストを持っている必要があります。これは一般的には問題にならないので、このオプションを有効にしてください。また、`GameplayTagContainers`は反復処理のために`TArray<FGameplayTag>`を返すことができます。

`FGameplayTagCountContainer`に格納された`GameplayTags`は、その`GameplayTag`のインスタンスの数を格納する`TagMap`を持ちます。`FGameplayTagCountContainer`はまだ`GameplayTag`を持っているかもしれないが、その`TagMapCount`は0である。`ASC`がまだ `GameplayTag` を持っている場合、デバッグ中にこれに遭遇する可能性がある。`HasTag()`や`HasMatchingTag()`などの関数は`TagMapCount`をチェックし、`GameplayTag`が存在しない場合や`TagMapCount`がゼロの場合はfalseを返す。

`GameplayTags`は、事前に`DefaultGameplayTags.ini`で定義しておく必要があります。UE4 Editorでは、開発者が`DefaultGameplayTags.ini`を手動で編集することなく、`GameplayTags`を管理できるように、プロジェクト設定にインターフェースを提供しています。`GameplayTag`エディターは、`GameplayTags`の作成、名前の変更、参照の検索、削除が可能です。

![プロジェクト設定のGameplayTagエディタ](https://github.com/dohi/GASDocumentation/raw/master/Images/gameplaytageditor.png)

`GameplayTag`の参照を検索すると、おなじみの`Reference Viewer`のグラフがエディタに表示され、`GameplayTag`を参照しているすべてのアセットが表示される。ただし、`GameplayTag`を参照しているC++クラスは表示されません。

`GameplayTags`の名前を変更すると、リダイレクトが作成されるので、元の`GameplayTag`を参照しているアセットは新しい`GameplayTag`にリダイレクトすることができます。可能であれば、新しい`GameplayTag`を作成して、すべての参照を新しい`GameplayTag`に手動で更新してから、古い`GameplayTag`を削除して、リダイレクトが発生しないようにしてください。

`GameplayTag`エディターには、`Fast Replication`に加えて、一般的に複製される`GameplayTags`を埋めて、さらに最適化するオプションがあります。

`GameplayTags` は `GameplayEffect` から追加された場合にはレプリケートされます。`ASC`ではレプリケートされない`LooseGameplayTags`を追加することができ、手動で管理する必要があります。サンプルプロジェクトでは、`State.Dead`の`LooseGameplayTag`を使用して、所有するクライアントのヘルスがゼロになったときにすぐに対応できるようにしています。リスポーンすると、手動で`TagMapCount`をゼロに戻します。`LooseGameplayTags`を使用する場合にのみ、`TagMapCount`を手動で調整する。手動で`TagMapCount`を調整するよりも、`UAbilitySystemComponent::AddLooseGameplayTag()`や`UAbilitySystemComponent::RemoveLooseGameplayTag()`の関数を使用する方が望ましいです。

C++で`GameplayTag`への参照を取得するには：
```c++
FGameplayTag::RequestGameplayTag(FName("Your.GameplayTag.Name"))
```

親や子の`GameplayTags`を取得するような高度な`GameplayTag`の操作については、`GameplayTagManager`が提供する関数を参照すること。GameplayTagManagerにアクセスするには、`GameplayTagManager.h`をインクルードし、`UGameplayTagManager::Get().FunctionName`で呼び出す。`GameplayTagManager`は、常に文字列を操作したり比較したりするよりも高速に処理できるように、`GameplayTags`をリレーショナル・ノード（親、子など）として実際に保存する。

`GameplayTags` と `GameplayTagContainers` は、オプションで `UPROPERTY` 指定子 `Meta = (Categories = "GameplayCue")` を持つことができ、ブループリント内のタグをフィルタリングして、親タグが `GameplayCue` である `GameplayTags` のみを表示します。これは、`GameplayTag`や`GameplayTagContainer`変数が`GameplayCue`に対してのみ使用されるべきであることがわかっている場合に便利です。

また、`FGameplayCueTag`という別の構造体があり、これは`FGameplayTag`をカプセル化し、ブループリント内の`GameplayTags`を自動的にフィルターして、`GameplayCue`の親タグを持つタグのみを表示するようにします。

関数の中で`GameplayTag`のパラメータをフィルタリングしたい場合は、`UFUNCTION`の指定子である`Meta = (GameplayTagFilter = "GameplayCue")`を使用します。関数内の`GameplayTagContainer`パラメータはフィルタリングできません。これを可能にするためにエンジンを編集したい場合は、`Engine\Plugins\EditorGameplayTagsEditorSource\GameplayTagsEditorPrivate\SGameplayTagGraphPin.cpp`の`SGameplayTagGraphPin::ParseDefaultValueData()`の呼び出し方を見てください。cppでは、`FilterString = UGameplayTagsManager::Get().GetCategoriesMetaFromField(PinStructType);`を呼び出し、`SGameplayTagGraphPin::GetListContent()`の`SGameplayTagWidget`に`FilterString`を渡している。これらの関数のうち、`Engine\Plugins\Editor\GamagplayTagsEditor\Source\GamagplayTagsEditor\Private\SGameplayTagContainerGraphPin.cpp`にある`GameplayTagContainer`バージョンでは、メタフィールドのプロパティをチェックせず、フィルターを渡している。

サンプルプロジェクトでは、`GameplayTags`が多用されています。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gt-change"></a>
### 4.2.1 ゲームプレイタグの変更への対応
`ASC`では、`GameplayTags`が追加・削除された際のデリゲートを提供している。これは、`GameplayTag`が追加・削除されたときや、`GameplayTag`の`TagMapCount`が変更されたときにのみ発火するように指定できる`EGameplayTagEventType`を受け取るものである。

```c++
AbilitySystemComponent->RegisterGameplayTagEvent(FGameplayTag::RequestGameplayTag(FName("State.Debuff.Stun")), EGameplayTagEventType::NewOrRemoved).AddUObject(this, &AGDPlayerState::StunTagChanged);
```

コールバック関数には、`GameplayTag`と新しい`TagCount`のパラメータがあります。
```c++
virtual void StunTagChanged(const FGameplayTag CallbackTag, int32 NewCount);
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a"></a>
### 4.3 Attributes

<a name="concepts-a-definition"></a>
#### 4.3.1 アトリビュートの定義
`属性`は構造体[`FGameplayAttributeData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayAttributeData/index.html)によって定義される浮動小数点値です。キャラクタのレベルに対するヘルスの量や、ポーションのチャージ数などを表すことができます。もしそれが `Actor` に属するゲームプレイ関連の数値であれば、`Attribute` の使用を検討すべきです。`属性`は一般的に、ASCがその変化を予測できるように、[`GameplayEffects`](#concepts-ge)によってのみ変更されるべきです。

属性は[`AttributeSet`](#concepts-as)によって定義され、その中に存在します。`AttributeSet`は複製用にマークされた`Attribute`を複製する責任があります。`Attributes`の定義方法については、[`AttributeSets`](#concepts-as)のセクションを参照してください。

**Tip:** エディタの`Attribute`リストに`Attribute`を表示させたくない場合は、`Meta = (HideInDetailsView)`プロパティ指定子を使用することができます。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-value"></a>
#### 4.3.2 ベース値とカレント値の比較
`Attribute`は`BaseValue`と`CurrentValue`の2つの値で構成されます。`BaseValue`は`Attribute`の永続的な値であり、`CurrentValue`は`BaseValue`に`GameplayEffects`による一時的な変更を加えたものです。例えば、あなたの`キャラクター`は、`BaseValue`が600ユニット/秒の`movespeed`属性を持っているとします。まだ移動速度を変更する`GameplayEffects`がないので、`CurrentValue`も600u/sになっています。もし一時的に50 u/sの移動速度バフを得た場合、`BaseValue`は600 u/sのままですが、`CurrentValue`は600 + 50で合計650 u/sとなります。移動速度バフが切れると、 `CurrentValue` は `BaseValue` である600u/sに戻ります。

よくGAS初心者の方が、 `BaseValue` を `Attribute` の最大値と混同して、そのように扱おうとすることがあります。これは間違ったアプローチです。変化する可能性があったり、アビリティやUIで参照される`Attribute`の最大値は、別の`Attribute`として扱うべきです。ハードコードされた最大値と最小値については、最大値と最小値を設定できる `FAttributeMetaData` を持つ `DataTable` を定義する方法がありますが、構造体の上にある Epic のコメントでは、`WIP`となっています。詳細は `AttributeSet.h` を参照してください。混乱を防ぐために、アビリティやUIで参照できる最大値は別の`Attributes`として作り、`Attributes`のクランプにのみ使用されるハードコードされた最大値と最小値は`AttributeSet`の中でハードコードされた浮動小数点として定義することをお勧めします。`Attributes`のクランプについて、`CurrentValue`への変更は[PreAttributeChange()](#concepts-as-preattributechange)、`GameplayEffects`による`BaseValue`への変更は[PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)で説明します。

`Instant` `GameplayEffect`は`BaseValue`を恒久的に変化させ、`Duration`と`Infinite` `GameplayEffect`は`CurrentValue`を変化させます。`Periodic` `GameplayEffects`は`Instant` `GameplayEffects`と同様に扱われ、`BaseValue`を変更します。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-meta"></a>
#### 4.3.3 Meta Attributes: メタ属性
いくつかの `Attributes` は、`Attributes` と相互作用することを意図した一時的な値のプレースホルダーとして扱われます。これを`メタ属性`と呼びます。例えば、一般的にはダメージを`メタ属性`として定義します。`GameplayEffect` がヘルスの`Attribute`を直接変更する代わりに、`damage`という`メタ属性`をプレースホルダーとして使用します。これにより、ダメージの値は[`GameplayEffectExecutionCalculation`](#concepts-ge-ec)の中でバフやデバフを使って修正することができ、さらに`AttributeSet`の中で操作することができます。例えば、現在のシールドの`Attribute`からダメージを引いて、最後に残りをヘルスの`Attribute`から引くことができます。ダメージの`メタ属性`は`GameplayEffects`の間では持続性がなく、すべての`メタ属性`によって上書きされます。`メタ属性`は通常、複製されません。

`メタ属性`はダメージやヒーリングのようなものに対して、「どれだけのダメージを与えたか」と「このダメージで何をするか」を論理的に分離するのに適しています。この論理的な分離は、我々の`GameplayEffects`や`Execution Calculations`が、ターゲットがダメージをどのように処理するかを知る必要がないことを意味します。ダメージの例を続けると、`GameplayEffects`がどれだけのダメージを与えるかを決定し、次に`AttributeSet`がそのダメージをどうするかを決定します。すべてのキャラクターが同じ`Attribute`を持つとは限りません。特にサブクラス化された`AttributeSet`を使用する場合はそうです。ベースの `AttributeSet` クラスはヘルスの `Attribute` しか持たないかもしれませんが、サブクラス化された `AttributeSet` はシールドの `Attribute` を追加することができます。盾の属性を持つサブクラス化された `AttributeSet` は、受けたダメージをベースの `AttributeSet` クラスとは違った形で分配します。

`メタ属性` は良いデザインパターンですが、必須ではありません。すべてのダメージのインスタンスに使用される `Execution Calculation` が1つだけで、すべてのキャラクターで共有される `AttributeSet` クラスが1つだけの場合は、ヘルスやシールドなどへのダメージ分配を `Execution Calculation` の中で行い、それらの `Attribute` を直接変更するのが良いでしょう。柔軟性が犠牲になるだけですが、それでいいと思います。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-changes"></a>
#### 4.3.4 属性の変更への対応
UIや他のゲームプレイを更新するために`Attribute`が変更されたときにリッスンするには、`UAbilitySystemComponent::GetGamaplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`を使用します。この関数は、バインドできるデリゲートを返します。このデリゲートは、`Attribute`が変更されるたびに自動的に呼び出されます。このデリゲートは、`NewValue`, `OldValue`, `FGameplayEffectModCallbackData`を含む`FOnAttributeChangeData`パラメータを提供します。

**注意** `FGameplayEffectModCallbackData` はサーバー上でのみ設定されます。

```c++
AbilitySystemComponent->GetGameplayAttributeValueChangeDelegate(AttributeSetBase->GetHealthAttribute()).AddUObject(this, &AGDPlayerState::HealthChanged);
```

```c++
virtual void HealthChanged(const FOnAttributeChangeData& Data);
```

サンプルプロジェクトでは、`Attribute`値を変更した`GDPlayerState`上のデリゲートにバインドして、HUDを更新したり、ヘルスがゼロになったときにプレイヤーの死に応答したりします。

これを `ASyncTask` にラップしたカスタム ブループリント ノードがサンプル プロジェクトに含まれています。これは `UI_HUD` UMG ウィジェットで使用され、ヘルス、マナ、スタミナの値を更新します。この `AsyncTask` は、手動で `EndTask()` が呼ばれるまで永遠に存続しますが、これは UMG Widget の `Destruct` イベントで行います。AsyncTaskAttributeChanged.h/cpp`を参照してください。

![Listen for Attribute Change BP Node](https://github.com/dohi/GASDocumentation/raw/master/Images/attributechange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-derived"></a>
#### 4.3.5 Derived Attributes: 派生型属性
1つまたは複数の他の `Attribute` から値の一部またはすべてを派生させた `Attribute` を作成するには、1つまたは複数の `Attribute Based` または [`MMC`](#concepts-ge-mmc) [`Modifiers`](#concepts-ge-mods)を使用した `Infinite` `GameplayEffect` を使用します。`Derived Attribute`は、依存する `Attribute` が更新されると自動的に更新されます。

`Derived Attribute` のすべての `Modifier` の最終的な計算式は、 `Modifier Aggregators` の計算式と同じです。一定の順序で計算を行う必要がある場合には、すべてを `MMC` の中で行います。

```
((CurrentValue + Additive) * Multiplicitive) / Division
```

**注意:** PIEで複数クライアントでプレイする場合、エディタ設定で `Run Under One Process`を無効にする必要があります。そうしないと、最初のクライアント以外のクライアントで、独立した`Attributes`が更新されても、 `Derived Attributes` は更新されません。

この例では、`Infinite` の `GameplayEffect` が、`TestAttrA = (TestAttrA + TestAttrB) * ( 2 * TestAttrC)` という式で、`Attribute` の `TestAttrB` と `TestAttrC` から `TestAttrA` の値を導き出しています。`TestAttrA` は，いずれかの `Attribute` が値を更新するたびに，その値を自動的に再計算します。

![Derived Attribute Example](https://github.com/dohi/GASDocumentation/raw/master/Images/derivedattribute.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as"></a>
### 4.4 Attribute Set

<a name="concepts-as-definition"></a>
`AttributeSet`は`Attribute`の変更を定義、保持、管理します。開発者は[`UAttributeSet`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAttributeSet/index.html)のサブクラスを作成する必要があります。`OwnerActor`のコンストラクタで`AttributeSet`を作成すると，自動的にその`ASC`に登録されます．**これはC++で行う必要があります**。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-design"></a>
#### 4.4.2 属性セットの設計
`ASC`は1つまたは複数の`AttributeSets`を持つことができます。`アトリビュートセット`はメモリのオーバーヘッドがほとんどないので、いくつの`アトリビュートセット`を使うかは開発者に任されています。

ゲーム内のすべての`Actor`が共有する1つの大きなモノリシックな`AttributeSet`を持ち、必要な場合にのみアトリビュートを使用し、未使用のアトリビュートは無視することも可能です。

また、必要に応じて `Actor` に選択的に追加する `Attribute` のグループを表す複数の `AttributeSet` を持つこともできます。例えば、ヘルス関連の `AttributeSet` や、マナ関連の `AttributeSet` などが考えられます。MOBAゲームでは、ヒーローはマナを必要としますが、ミニオンは必要ありません。そのため、ヒーローはマナ用の`AttributeSet`を取得し、ミニオンは取得しません。

さらに、`AttributeSets`は、`Actor`が持つ`Attribute`を選択的に選択する別の手段として、サブクラス化することができます。`Attribute`は内部的には`AttributeSetClassName.AttributeName`と呼ばれます。`AttributeSet`をサブクラス化すると、親クラスのすべての`Attribute`は親クラスの名前をプレフィックスとして持ちます。

複数の `AttributeSet` を持つことができますが、1つの `ASC` に同じクラスの `AttributeSet` を複数持つべきではありません。同じクラスの`AttributeSet`が複数あると、どの`AttributeSet`を使えばいいのかわからなくなり、1つを選んでしまいます。

<a name="concepts-as-design-subcomponents"></a>
##### 4.4.2.1 個々の属性を持つサブコンポーネント
個別にダメージを与えることができるアーマーピースのような、`Pawn`に複数のダメージを与えることができるコンポーネントがある場合、ダメージを与えることができるコンポーネントの最大数がわかっているのであれば、それらのコンポーネントの論理的な`Slot`を表すために、1つの`AttributeSet`に、DamageableCompHealth0、DamageableCompHealth1などのヘルスの`Attribute`をたくさん作ることをお勧めします。ダメージを受けやすいコンポーネントのクラスインスタンスには、スロット番号である`Attribute`を割り当てます。これは`GameplayAbilities`や[`Executions`](#concepts-ge-ec)で読み取ることができ、どの`Attribute`にダメージを適用するかを知ることができます。ダメージを与えることができるコンポーネントの数が最大値よりも少ないか、ゼロである`Pawn`は問題ありません。`AttributeSet`に`Attribute`が含まれているからといって、それを使わなければならないわけではありません。未使用の `Attribute` は些細な量のメモリを消費します。

サブコンポーネントがそれぞれ多くの `Attribute` を必要とする場合や、サブコンポーネントの数が無限大になる可能性がある場合、サブコンポーネントが切り離されて他のプレイヤーに使用される可能性がある場合 (例: 武器)、またはその他の理由でこのアプローチがうまくいかない場合は、`Attribute` をやめて、代わりにコンポーネントに普通の浮動小数点数を保存することをお勧めします。[Item Attributes](#concepts-as-design-itemattributes)を参照してください。

<a name="concepts-as-design-addremoveruntime"></a>
##### 4.4.2.2 ランタイムでのAttributeSetsの追加と削除
`AttributeSets` は実行時に `ASC` に追加したり削除したりすることができます。しかし、`AttributeSets` を削除することは危険な場合があります。例えば、サーバーよりも先にクライアントで `AttributeSet` が削除され、`Attribute` の値の変更がクライアントにレプリケートされると、`Attribute` がその `AttributeSet` を見つけられず、ゲームがクラッシュします。

武器がインベントリに追加されたとき：
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().AddUnique(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```

武器がインベントリから削除されたとき：
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().Remove(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```
<a name="concepts-as-design-itemattributes"></a>
##### 4.4.2.3 Item Attributes (Weapon Ammo)
装備可能なアイテムに`Attributes`を実装する方法はいくつかあります(武器の弾薬、アーマーの耐久性など)。これらの方法はすべて、アイテムに直接値を保存します。これは、生涯にわたって複数のプレイヤーが装備可能なアイテムの場合には必要です。

> 1. アイテム上でプレーンなフロートを使用する(**推奨**)
> 1. アイテム上で `AttributeSet` を分離する。
> 1. アイテム上で `ASC` を分離する。

<a name="concepts-as-design-itemattributes-plainfloats"></a>
###### 4.4.2.3.1 アイテム上のプレーンフロート
`Attributes`の代わりに、アイテムクラスのインスタンスにplain floatの値を格納します。Fortniteや[GASShooter](https://github.com/dohi/GASShooter)では、この方法で銃の弾薬を扱っています。銃の場合は、最大クリップサイズ、クリップ内の現在の弾薬、予備弾薬などを、複製された浮動小数点数 (`COND_OwnerOnly`) として、銃のインスタンスに直接格納します。武器が予備弾薬を共有している場合は、予備弾薬を共有弾薬の`AttributeSet`の`Attribute`としてキャラクターに移動させます(リロード能力は、`Cost GE`を使用して、予備弾薬から銃のフロートクリップ弾薬に引き込むことができます)。現在のクリップ弾薬に `Attribute` を使用していないので、銃のフロート弾薬に対してコストをチェックして適用するために、`UGameplayAbility` のいくつかの関数をオーバーライドする必要があります。アビリティを付与する際に[`GameplayAbilitySpec`](https://github.com/dohi/GASDocumentation#concepts-ga-spec)で銃を`SourceObject`にすることで、アビリティの中でアビリティを付与した銃にアクセスできるようになります。

自動射撃中に銃が弾薬量を複製してローカルの弾薬量を奪うのを防ぐために、プレイヤーが `PreReplication()` で `IsFiring` `GameplayTag` を持っている間は複製を無効にします。ここでは基本的に自分自身のローカル予測を行っています。

```c++
void AGSWeapon::PreReplication(IRepChangedPropertyTracker& ChangedPropertyTracker)
{
	Super::PreReplication(ChangedPropertyTracker);

	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, PrimaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, SecondaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
}
```

メリット
1. `AttributeSets`を使用する際の制限を回避することができる（下記参照）

デメリット
1. 既存の`GameplayEffect`のワークフローを使用できない(弾薬を使用するための`Cost GEs`など)
1. 銃のフロートに対する弾薬のコストをチェックして適用するために、`UGameplayAbility`の主要な関数をオーバーライドする作業が必要。

<a name="concepts-as-design-itemattributes-attributeset"></a>
##### 4.4.2.3.2 アイテムの`AttributeSet`について
アイテムに個別の`AttributeSet`を使用すると、[プレイヤーのインベントリに追加する際にプレイヤーの`ASC`に追加される](#concepts-as-design-addremoveruntime)ようになりますが、いくつかの大きな制限があります。私は[GASShooter](https://github.com/dohi/GASShooter)の初期のバージョンで、武器の弾薬にこれを使っていました。武器は、最大クリップサイズ、クリップ内の現在の弾薬、予備弾薬などの`Attribute`を、武器クラスにある`AttributeSet`に格納します。武器が予備弾薬を共有している場合は、予備弾薬を共有弾薬の `AttributeSet` に入れてキャラクターに移動させます。サーバー上でプレイヤーのインベントリに武器が追加されると、その武器はプレイヤーの `ASC::SpawnedAttributes` にその `AttributeSet` を追加します。サーバーはこの情報をクライアントに伝えます。武器がインベントリから削除されると、その武器は `ASC::SpawnedAttributes` から `AttributeSet` を削除します。

`AttributeSet`が`OwnerActor`以外の何か（例えば武器）に常駐している場合、最初は`AttributeSet`にいくつかのコンパイルエラーが発生します。修正方法は、コンストラクタではなく、`BeginPlay()`で`AttributeSet`を構築し、`IAbilitySystemInterface`を武器に実装する（武器をプレイヤーのインベントリに追加するときに`ASC`へのポインタを設定する）ことです。

```c++
void AGSWeapon::BeginPlay()
{
	if (!AttributeSet)
	{
		AttributeSet = NewObject<UGSWeaponAttributeSet>(this);
	}
	//...
}
```

この[古いバージョンのGASShooter](https://github.com/dohi/GASShooter/tree/df5949d0dd992bd3d76d4a728f370f2e2c827735)をチェックすることで、実際に見ることができます。

メリット
1. 既存の`GameplayAbility`と`GameplayEffect`のワークフローを利用できる（弾薬使用のための`Cost GEs`など）。
1. 非常に小さなアイテムのためのセットアップが簡単にできる

デメリット
1. 武器の種類ごとに新しい `AttributeSet` クラスを作成する必要があります。なぜなら、`Attribute`への変更は、`ASC`の`SpawnedAttributes`配列の中で、その`AttributeSet`クラスの最初のインスタンスを探すからです。同じ `AttributeSet` クラスの追加インスタンスは無視されます。
1.  プレイヤーのインベントリには、各タイプの武器を1つずつしか入れることができません。これは、以前の理由により、1つの`AttributeSet`クラスにつき1つの`AttributeSet`インスタンスしかないためです。
1.  `AttributeSet`を削除するのは危険です。古いGASShooterでは、プレイヤーがロケットで自殺した場合、プレイヤーはすぐにロケットランチャーをインベントリから削除しました（その`AttributeSet`を`ASC`からも削除しました）。ロケットランチャーの弾薬の `Attribute` が変更されたことがサーバーにレプリケートされると、`AttributeSet` がクライアントの `ASC` に存在しなくなり、ゲームがクラッシュしました。

<a name="concepts-as-design-itemattributes-asc"></a>
###### 4.4.2.3.3 `ASC` on the Item
各アイテムに `AbilitySystemComponent` 全体を配置するのは、極端なアプローチです。私は個人的にこれを行ったことはありませんし、使用されているものを見たこともありません。これを実現するには多くのエンジニアリングが必要になるでしょう。

> オーナーが同じでアバターが異なる複数のAbilitySystemComponentを持つことは可能ですか？
> 
> 私が考える最初の問題は、所有するアクターにIGameplayTagAssetInterfaceとIAbilitySystemInterfaceを実装することです。前者は可能かもしれません。すべてのASCからタグを集約すればいいのです（ただし、「HasAllMatchingGameplayTags」はASCをまたいだ集約によってのみ満たされる可能性があるので注意が必要です）。その呼び出しを各ASCに転送して、結果を一緒にORするだけでは十分ではないでしょう）。) しかし、後者はさらに厄介で、どのASCが権威あるものなのか？誰かがGEを適用したいと思ったとき、どのASCがそれを受け取るべきなのか？これらを解決できるかもしれませんが、オーナーが複数のASCを傘下に持つという点では、こちらの問題の方が難しいでしょう。
> 
> ポーンと武器のASCを分けることは、それだけで意味があります。例えば、武器を説明するタグと、所有するポーンを説明するタグを区別することができます。武器に付与されたタグは所有者にも「適用」され、それ以外には適用されないというのも意味があるかもしれません（例えば、属性とGEは独立していますが、所有者は上述のように所有しているタグを集約します）。これはうまくいくと思いますよ。しかし、同じオーナーを持つ複数のASCを持つことは、危険なことかもしれません。

*Dave Ratti from Epic's answer to [community questions #6](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)*

メリット
1. 既存の `GameplayAbility` と `GameplayEffect` のワークフローを使用することができます（弾薬使用のための "Cost GE "など）。
1. `AttributeSet` クラスを再利用できる(各武器のASCに1つずつ)

デメリット
1. 不明なエンジニアリングコスト
1. それは可能なのか？

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-attributes"></a>
#### 4.4.3 属性の定義
**`Attribute`はC++で、`AttributeSet`のヘッダファイルでのみ定義できます。** すべての`AttributeSet`ヘッダーファイルの先頭に、以下のマクロのブロックを追加することをお勧めします。これにより、`Attribute`のゲッター関数とセッター関数が自動的に生成されます。

```c++
// Uses macros from AttributeSet.h
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)
```

レプリケートされるヘルスの属性はこのように定義できます：

```c++
UPROPERTY(BlueprintReadOnly, Category = "Health", ReplicatedUsing = OnRep_Health)
FGameplayAttributeData Health;
ATTRIBUTE_ACCESSORS(UGDAttributeSetBase, Health)
```

ヘッダに`OnRep`関数も定義します：

```c++
UFUNCTION()
virtual void OnRep_Health(const FGameplayAttributeData& OldHealth);
```

`AttributeSet`の.cppファイルは、`OnRep`関数に、予測システムが使用する`GAMEPLAYATTRIBUTE_REPNOTIFY`マクロを記入する必要があります。
```c++
void UGDAttributeSetBase::OnRep_Health(const FGameplayAttributeData& OldHealth)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UGDAttributeSetBase, Health, OldHealth);
}
```

最後に、`GetLifetimeReplicatedProps`に`Attribute`を追加する必要があります。
```c++
void UGDAttributeSetBase::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);

	DOREPLIFETIME_CONDITION_NOTIFY(UGDAttributeSetBase, Health, COND_None, REPNOTIFY_Always);
}
```

`REPNOTIFY_Always`は、ローカルの値がサーバーからレプリケートされてきた値とすでに等しい場合に（予測によりそうなる場合に）でも、`OnRep`関数をトリガーするように指示します。デフォルトでは、ローカルの値がサーバーからレプリケートされてきた値と同じであれば、`OnRep`関数はトリガーされません。

もし`Attribute`が`Meta Attribute`のように複製されていなければ、`OnRep`と`GetLifetimeReplicatedProps`のステップは省略することができます。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-init"></a>
#### 4.4.4 属性の初期化
アトリビュートを初期化するには、複数の方法があります (アトリビュートの `BaseValue` や `CurrentValue` を初期値に設定する)。Epic では、インスタントの `GameplayEffect` を使用することをお勧めします。これは、サンプル プロジェクトでも使用されている方法です。

`Attributes`を初期化するためにインスタントの`GameplayEffect`を作成する方法については、サンプルプロジェクトの`GE_HeroAttributes`ブループリントを参照してください。この`GameplayEffect`の適用はC++で行われます。

`Attribute`を定義する際に`ATTRIBUTE_ACCESSORS`マクロを使用した場合は、各`Attribute`の`AttributeSet`上に初期化関数が自動的に生成されますので、C++で自由に呼び出すことができます。

```c++
// InitHealth(float InitialValue) is an automatically generated function for an Attribute 'Health' defined with the `ATTRIBUTE_ACCESSORS` macro
AttributeSet->InitHealth(100.0f);
```

`Attributes`を初期化するその他の方法については `AttributeSet.h` を参照してください。（訳注：DataTableからのインポート。InitFromMetaDataTable）

**注意:** 4.24より前のバージョンでは、`FAttributeSetInitterDiscreteLevels`は`FGameplayAttributeData`では動作しませんでした。これは `Attributes` が生の浮動小数点であったときに作成されたもので、`FGameplayAttributeData` が `Plain Old Data` (`POD`) ではないことが原因でした。これは4.24 https://issues.unrealengine.com/issue/UE-76557 で修正されています。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-preattributechange"></a>
#### 4.4.5 PreAttributeChange()
`PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)`は`AttributeSet`内の主要な関数の一つで、`Attribute`の`CurrentValue`への変更時に、変更が起こる前に対応するためのものです。この関数は、参照パラメータ `NewValue` を介して `CurrentValue` への変更をクランプするのに理想的な場所です。

例えば、movespeedモディファイアを固定するために、サンプルプロジェクトでは次のようにしています。
```c++
if (Attribute == GetMoveSpeedAttribute())
{
	// Cannot slow less than 150 units/s and cannot boost more than 1000 units/s
	NewValue = FMath::Clamp<float>(NewValue, 150, 1000);
}
```
`GetMoveSpeedAttribute()`関数は、`AttributeSet.h` ([Defining Attributes](#concepts-as-attributes))に追加したマクロブロックによって作成されます。

これは、`Attribute`のセッター(`AttributeSet.h` ([Defining Attributes](#concepts-as-attributes))のマクロブロックで定義されている)を使用しているか、[`GameplayEffects`](#concepts-ge)を使用しているかに関わらず、`Attributes`に変更があった場合にトリガーされます。

**注意:** ここで発生するクランプは、`ASC`のモディファイアを恒久的に変更するものではありません。モディファイアのクエリから返される値を変更するだけです。つまり、[`GameplieEffectExecutionCalculations`](#concepts-ge-ec)や[`ModifierMagnitudeCalculations`](#concepts-ge-mmc)のように、すべてのモディファイアから`CurrentValue`を再計算するものは、クランピングを再度実装する必要があります。

**注意:** Epicの`PreAttributeChange()`のコメントによると、ゲームプレイイベントには使用せず、主にclampingに使用するとのことです。`UAbilitySystemComponent::GetGamaplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`（[属性変更への対応](#concepts-a-changes)）が、`Attribute`変更時のゲームプレイイベントに推奨される場所です。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-postgameplayeffectexecute"></a>
#### 4.4.6 PostGameplayEffectExecute()
`PostGameplayEffectExecute(const FGameplayEffectModCallbackData & Data)`は、インスタントの[`GameplayEffect`](#concepts-ge)から`Attribute`の`BaseValue`が変更された後にのみトリガーされます。これは、`GameplayEffect`から`Attribute`が変化したときに、さらに`Attribute`を操作するための有効な場所です。

例えば、サンプルプロジェクトでは、ここでヘルスの`属性`から最終的なダメージの`Meta属性`を引いています。もし、シールドの属性があった場合は、まずシールドのダメージを引いてから、残りをヘルスから引きます。サンプルプロジェクトでは、この場所を使って、ヒットリアクションアニメーションを適用したり、フローティングダメージナンバーを表示したり、殺人者に経験値やゴールドバウンティを割り当てたりしています。デザイン上、ダメージの`Meta Attribute`は常にインスタントの`GameplayEffect`を介して行われ、`Attribute`セッターではありません。

マナやスタミナのように、インスタントの`GameplayEffects`から`BaseValue`が変更されるだけの他の`Attribute`も、ここでその最大値に対応する`Attribute`に固定することができます。

**注意:** `PostGameplayEffectExecute()`が呼ばれたとき、`Attribute`への変更はすでに行われていますが、まだクライアントにはレプリケートされていないので、ここで値をクランプしても、クライアントに2回のネットワークアップデートが行われることはありません。クライアントはクランピング後にのみ更新を受け取ります。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-onattributeaggregatorcreated"></a>
#### 4.4.7 OnAttributeAggregatorCreated()
`OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator)`は、このセット内の`Attribute`に対して`Aggregator`が作成されたときにトリガーします。これにより、[`AggregatorEvaluateMetaData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FAggregatorEvaluateMetaData/index.html)のカスタム設定が可能になります。`AggregatorEvaluateMetaData` は、`Attribute` に適用されたすべての [`Modifiers`](#concepts-ge-mod) に基づいて `CurrentValue` を評価する際に `Aggregator` が使用します。デフォルトでは、`AggregatorEvaluateMetaData`は、`Aggregator`がどの`修飾子`が適格かを判断するためにのみ使用されます。例えば、`MostNegativeMod_AllPositiveMods`は、すべてのポジティブな`修飾子`を許可しますが、ネガティブな`修飾子`は最もネガティブなものだけに制限されます。これはParagonが使用したもので、すべてのポジティブな移動速度バフを適用する一方で、同時にいくつのスロー効果があるかに関わらず、最もネガティブな移動速度スロー効果のみをプレイヤーに適用できるようにしたものです。条件を満たさない`Modifier`は`ASC`上にまだ存在し、最終的な`CurrentValue`に集約されていないだけです。最もマイナスの「修飾子」が期限切れになった場合、次にマイナスの「修飾子」（存在する場合）が適用されるというように、条件が変われば後に適用される可能性があります。

AggregatorEvaluateMetaData を、最も否定的な `Modifier` とすべての肯定的な `Modifier` のみを許可する例で使用するには、以下のようになります。

```c++
virtual void OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const override;
```

```c++
void UGSAttributeSetBase::OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const
{
	Super::OnAttributeAggregatorCreated(Attribute, NewAggregator);

	if (!NewAggregator)
	{
		return;
	}

	if (Attribute == GetMoveSpeedAttribute())
	{
		NewAggregator->EvaluationMetaData = &FAggregatorEvaluateMetaDataLibrary::MostNegativeMod_AllPositiveMods;
	}
}
```

`AggregatorEvaluateMetaData`に修飾子を追加する場合は、`FAggregatorEvaluateMetaDataLibrary` に静的変数として追加します。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge"></a>
### 4.5 Gameplay Effects: ゲームプレイエフェクト

<a name="concepts-ge-definition"></a>
#### 4.5.1 ゲームプレイ効果の定義
[`ゲームプレイ効果`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffect/index.html) (`GE`)とは、アビリティが自分や他人の[`属性`](#concepts-a)や[`ゲームプレイタグ`](#concepts-gt)を変化させるための器です。アビリティは、ダメージやヒーリングのような即時的な属性変化を引き起こしたり、移動速度の向上やスタンディングのような長期的なステータスのバフ/デバフを適用することができます。`UGameplayEffect`クラスは、1つのゲームプレイ効果を定義する**データのみ**のクラスであることを意図しています。`GameplayEffects`には追加のロジックを加えてはいけません。一般的にデザイナーは、`UGameplayEffect`のブループリントの子クラスを多数作成します。

`GameplayEffects`は[`Modifiers`](#concepts-ge-mods)と[`Executions` (`GameaplieEffectExecutionCalculation`)](#concepts-ge-ec)によって属性を変更します。

`GameplayEffects`には、`Instant`、`Duration`、`Infinite`の3種類の持続時間があります。

さらに、`GameplayEffects`は[`GameplayCues`](#concepts-gc)を追加/実行することができます。`Instant`の`GameplayEffect`は`GameplayCue`の`GameplayTags`に対して`Execute`を呼び出し、`Duration`または`Infinite`の`GameplayEffect`は`GameplayCue`の`GameplayTags`に対して`Add`と`Remove`を呼び出します。

| Duration Type | GameplayCue Event | When to use                                                                                                                                                                                                                                |
| ------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Instant`     | Execute           | `Attribute`の`Base Value`を、即時、恒久的に変更します。`GameplayTags`は、１フレームでも適用されません。|
 `Duration`    | Add & Remove      | `Attribute`の`CurrentValue`を一時的に変更したり、`GameplayTags`を適用するためのものです。`GameplayEffect`が期限切れになるか、手動で削除されると削除されます。持続時間は `UGameplayEffect` クラス/ブループリントで指定されます。|
| `Infinite`    | Add & Remove      | `Attribute`の`CurrentValue`を一時的に変更したり、`GameplayEffect`が削除されたときに削除される`GameplayTags`を適用するためのものです。これらは単独では期限切れにならないので、アビリティや`ASC`によって手動で削除する必要があります。|

`Duration`と`Infinite`の`GameplayEffects`は、`Period`で定義される`X`秒ごとに`Modifiers`と`Executions`を適用する`Periodic Effects（周期的効果）`を適用するオプションがあります。`Periodic Effects` は、`Attribute`の`BaseValue`の変更や`GameplayCue`の実行に関しては、`Instant` `GameplayEffects`として扱われます。これらはDOT(Damage Over Time)タイプのエフェクトに有効です。 **注意：** `Periodic Effects` は[予測](#concepts-p)できません。

`Duration`と`Infinite`の`GameplayEffects`は、その`Ongoing Tag Requirements`が満たされていない場合、適用後に一時的にオフにしたりオンにしたりすることができます([Gameplay Effect Tags](#concepts-ge-tags))。`GameplayEffect`をオフにすると、その`Modifiers`や適用された`GameplayTags`の効果は除去されますが、`GameplayEffect`は除去されません。`GameplayEffect`を再びオンにすると、その`Modifiers`と`GameplayTags`が再び適用されます。

もし、`Duration`や`Infinite`の`GameplayEffect`の`Modifiers`を手動で再計算する必要がある場合は、`UAbilitySystemComponent::ActiveGameplayEffects.SetActiveGameplayEffects`を呼び出してください。`SetActiveGameplayEffectLevel(FActiveGameplayEffectHandle ActiveHandle, int32 NewLevel)`には、`UAbilitySystemComponent::ActiveGameplayEffects.GetActiveGameplayEffect(ActiveHandle).Spec.GetLevel()`を使って、すでに持っているレベルと同じレベルを設定することができます。バッキング`Attributes`に基づく`Modifiers`は、バッキング`Attributes`が更新されると自動的に更新されます。`Modifier`を更新するための`SetActiveGameplayEffectLevel()`の主な機能は以下の通りです：

```C++
MarkItemDirty(Effect);
Effect.Spec.CalculateModifierMagnitudes();
// Private function otherwise we'd call these three functions without needing to set the level to what it already is
UpdateAllAggregatorModMagnitudes(Effect);
```

`GameplayEffects`は通常、インスタンス化されません。アビリティや`ASC`が`GameplayEffect`を適用したいときは、`GameplayEffect`の`ClassDefaultObject`から[`GameplayEffectSpec`](#concepts-ge-spec)を作成します。正常に適用された`GameplayEffectSpec`は、`FActiveGameplayEffect`という新しい構造体に追加され、`ASC`が`ActiveGameplayEffects`という特別なコンテナ構造体に記録します。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-applying"></a>
#### 4.5.2 ゲームプレイ効果の適用
`GameplayEffects`は[`GameplayAbilities`](#concepts-ga)上の関数や`ASC`上の関数から様々な方法で適用することができ、通常は`ApplyGameplayEffectTo`の形をとります。これらの関数は基本的に便宜的なもので、最終的には `Target` 上で `UAbilitySystemComponent::ApplyGameplayEffectSpecToSelf()` を呼び出すことになります。

`GameplayAbility`の外で`GameplayEffects`を適用するには、`Target`の`ASC`を取得して、その関数の一つを使って`ApplyGameplayEffectToSelf`を行う必要があります。

`ASC`のデリゲートにバインドすることで、`Duration`または`Infinite`の`GameplayEffects`が適用されたときに、それをリッスンすることができます。
```c++
AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf.AddUObject(this, &APACharacterBase::OnActiveGameplayEffectAddedCallback);
```
コールバック関数：
```c++
virtual void OnActiveGameplayEffectAddedCallback(UAbilitySystemComponent* Target, const FGameplayEffectSpec& SpecApplied, FActiveGameplayEffectHandle ActiveHandle);
```

サーバーはレプリケーションモードに関わらず、常にこの関数を呼び出します。自律型プロキシ（Autonomous Proxy）は複製された`GameplayEffects`に対して、`Full`と`Mixed`のレプリケーションモードでのみ、この関数を呼び出します。シミュレートされたプロキシ（Simulated Proxy）は、`Full` [レプリケーションモード](#concepts-asc-rm)でのみ、これを呼び出します。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-removing"></a>
#### 4.5.3 ゲームプレイエフェクトの削除
`GameplayEffects`は、[`GameplayAbilities`](#concepts-ga)上の関数や`ASC`上の関数から様々な方法で取り除くことができ、通常は`RemoveActiveGameplayEffect`という形式をとります。これらの関数は基本的には便宜的なもので、最終的には `Target` 上の `FActiveGameplayEffectsContainer::RemoveActiveEffects()` を呼び出すことになります。

`GameplayAbility`の外で`GameplayEffects`を削除するには、`Target`の`ASC`を取得して、その中のいずれかの関数を使って`RemoveActiveGameplayEffect`を行う必要があります。

また、ある`ASC`から`Duration`や`Infinite`の`GameplayEffects`が削除されたときには、そのデリゲートにバインドすることでリッスンすることができます。
```c++
AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate().AddUObject(this, &APACharacterBase::OnRemoveGameplayEffectCallback);
```
コールバック関数：
```c++
virtual void OnRemoveGameplayEffectCallback(const FActiveGameplayEffect& EffectRemoved);
```

サーバーはレプリケーションモードに関わらず、常にこの関数を呼び出します。自律型プロキシは複製された`GameplayEffects`に対して、`Full`と`Mixed`のレプリケーションモードでのみ、この関数を呼び出します。シミュレートされたプロキシは、`Full` [レプリケーションモード](#concepts-asc-rm)でのみ、これを呼び出します。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods"></a>
#### 4.5.4 Gameplay Effect Modifiers: ゲームプレイ効果の修飾子
`Modifiers`は`Attribute`を変更するもので、`Attribute`を変更する唯一の方法です。1つの`GameplayEffect`は0個以上の`Modifier`を持つことができます。各 `Modifier` は指定された操作によって1つの `Attribute` のみを変更する責任があります。

| Operation  | Description                                                                                                         |
| ---------- | ------------------------------------------------------------------------------------------------------------------- |
| `Add`      | `Modifier`で指定された `Attribute` に結果を加算します。減算には負の値を使います。|
| `Multiply` | `Modifier`で指定された `Attribute` に乗算します。|
| `Divide`   | `Modifier`で指定された `Attribute` に除算します。|
| `Override` | `Modifier`で指定された `Attribute` を上書きします。|

ある `Attribute` の `CurrentValue` は、その `BaseValue` にすべての `Modifiers` を加えたものです。`Modifiers` がどのように集約されるかについては、`GameplayEffectAggregator.cpp`の`FAggregatorModChannel::EvaluateWithBase`で以下のように定義されています。
```c++
((InlineBaseValue + Additive) * Multiplicitive) / Division
```

すべての `Override` `Modifier` は、最後に適用された `Modifier` が優先され、最終的な値を上書きします。

**Note:** パーセンテージベースの変更では、必ず `Multiply` オペレーションを使用して、加算の後に変更を行うようにしてください。

**Note:** [Prediction](#concepts-p)ではパーセンテージによる変更に問題があります。

`Modifiers`には4つのタイプがあります。「スケーラブルフロート」、「属性ベース」、「カスタム計算クラス」、「呼び出し元で設定」です。これらはすべて、何らかのフロート値を生成し、それを使って `Modifier` の指定された `Attribute` を操作に応じて変更します。

| `Modifier` Type            | Description|
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Scalable Float`           | `FScalableFloats` は、変数を行、レベルを列としたデータテーブルを指すことができる構造体です。Scalable Floatsは、アビリティの現在のレベル(または[`GameplayEffectSpec`](#concepts-ge-spec)でオーバーライドされている場合は異なるレベル)で、指定されたテーブルの行の値を自動的に読み取ります。この値は、さらに係数で操作することができます。データテーブル/行が指定されていない場合は、値を1として扱いますので、係数を使ってすべてのレベルで単一の値をハードコーディングすることができます。![ScalableFloat](https://github.com/dohi/GASDocumentation/raw/master/Images/scalablefloats.png)|
| `Attribute Based`          | `Attribute Based` `Modifier`は、`Source`（`GameplayEffectSpec`を作成した人）または`Target`（`GameplayEffectSpec`を受け取った人）にある、バッキング`Attribute`の`Current Value`または`Base Value`を取得し、さらにそれを係数と前後の係数の追加で修正します。`Snapshotting`とは、`GameplayEffectSpec`が作成されたときにバッキング`Attribute`がキャプチャされることを意味し、`no snapshotting`とは、`GameplayEffectSpec`が適用されたときにバッキング`Attribute`がキャプチャされることを意味します。|
| `Custom Calculation Class` | `カスタム計算クラス`は複雑な`Modifiers`に対して最も柔軟性を提供します。この `Modifier` は [`ModifieMagnitudeCalculation`](#concepts-ge-mmc) クラスを取り、さらに係数や前後の係数の追加によって、結果として得られる浮動小数点値を操作することができます。|
| `Set By Caller`            | `SetByCaller` `Modifiers` は、アビリティや、`GameplayEffectSpec` の作成者によって、実行時に `GameplayEffect` の外側に設定される値です。例えば、プレイヤーがボタンを押してアビリティをチャージした時間に応じてダメージを設定したい場合は、`SetByCaller`を使用します。`SetByCallers`は基本的に`TMap<FGameplayTag, float>`で、`GameplayEffectSpec`の上に置かれます。`Modifier`は`Aggregator`に対して、与えられた`GameplayTag`に関連する`SetByCaller`の値を探すように指示するだけです。`Modifier`が使用する`SetByCallers`は、`GameplayTag`バージョンのコンセプトしか使用できません。ここでは`FName`バージョンは無効となります。もし`Modifier`が`SetByCaller`に設定されていても、正しい`GameplayTag`を持つ`SetByCaller`が`GameplayEffectSpec`に存在しない場合、ゲームはランタイムエラーを起こして0を返します。これは`Divide`操作の場合に問題を起こす可能性があります。`SetByCallers`の使い方の詳細は[SetByCallers](#concepts-ge-spec-setbycaller)を参照してください。|

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods-multiplydivide"></a>
##### 4.5.4.1 掛け算と割り算の修飾子
デフォルトでは、すべての`Multiply`と`Divide`の`Modifiers`は、`Attribute`の`BaseValue`に乗算または除算する前に、（訳注：SumModsによって）合計されます。

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Additive = SumMods(Mods[EGameplayModOp::Additive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Additive), Parameters);
	float Multiplicitive = SumMods(Mods[EGameplayModOp::Multiplicitive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Multiplicitive), Parameters);
	float Division = SumMods(Mods[EGameplayModOp::Division], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Division), Parameters);
	...
	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
	...
}
```

```c++
float FAggregatorModChannel::SumMods(const TArray<FAggregatorMod>& InMods, float Bias, const FAggregatorEvaluateParameters& Parameters)
{
	float Sum = Bias;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Sum += (Mod.EvaluatedMagnitude - Bias);
		}
	}

	return Sum;
}
```
*from `GameplayEffectAggregator.cpp`*.

この式では、`Multiply`と`Divide`の両方の`Modifier`が`Bias`値として`1`を持っています（`Addition`は`Bias`値として`0`を持っています）。つまり、次のようになります。

```
1 + (Mod1.Magnitude - 1) + (Mod2.Magnitude - 1) + ...
```

この式はいくつかの予想外の結果をもたらします。まず、この式では、すべての`Modifier`を加算してから、それを `BaseValue` に乗算または除算しています。ほとんどの人は、それらを一緒に掛けたり割ったりすることを期待するでしょう。例えば、2つの `Multiply` 修飾子が `1.5` であれば、多くの人は `BaseValue` に `1.5 x 1.5 = 2.25` が掛けられると考えるでしょう。そうではなく、これは `1.5` を足して `BaseValue` を `2` 倍しています (`50% 増加 + さらに 50% 増加 = 100% 増加`)（訳注：1 + 1.5-1 + 1.5-1 = 2.）。これは`GameplayPrediction.h`の例で、`500`のベーススピードに`10%`のスピードバフをかけると`550`になるというものです。さらに`10%`のスピードバフを追加すると`600`になります。

次に、この式はParagonを念頭に置いて設計されているため、どのような値を使用できるかについて、文書化されていないルールがあります。

`Multiply`と`Divide`の乗算加算式のルール。
* `(1つ以上の値 < 1) AND (任意の数の値 [1, 2))`
* `OR (1つの値 >= 2)`

式中の `Bias` は、基本的に `[1, 2)` の範囲にある数値の整数桁を差し引きます。最初の `Modifier` の `Bias` は、開始時の `Sum` の値 (ループの前に `Bias` に設定されている) から減算されます。これが、任意の値が単独で動作する理由であり、1つの値 `< 1` が範囲 `[1, 2)` の数字で動作する理由です。

`Multiply`の例をいくつか挙げてみましょう。

乗数： `0.5` の場合  
`1 + (0.5 - 1) = 0.5`, 正しい。

乗算器： `0.5, 0.5` の場合  
`1 + (0.5 - 1) + (0.5 - 1) = 0`, 不正解。 `1` を期待した？`1`未満の複数の値は、乗算器の追加には意味がありません。Paragon は [`Multiply` `Modifiers` の最大の負の値](#cae-nonstackingge) のみを使用するように設計されているため、`BaseValue` に乗じる `1` 未満の値は最大でも 1 つしかありません。

乗算器: `1.1, 0.5`.  
`1 + (0.5 - 1) + (1.1 - 1) = 0.6` となります。

乗数： `5, 5` の場合  
`1 + (5 - 1) + (5 - 1) = 9`, 正しくは `10` です。常に`修飾子の合計 - 修飾子の数 + 1`となります。

多くのゲームでは、`Modify`と`Divide`の`Modifier`を、`BaseValue`に適用する前に、掛け算と割り算にしたいと思うでしょう。これを実現するためには、**`FAggregatorModChannel::EvaluateWithBase()`のエンジンコード** を変更する必要があります。

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Multiplicitive = MultiplyMods(Mods[EGameplayModOp::Multiplicitive], Parameters);
	...

	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
}
```

```c++
float FAggregatorModChannel::MultiplyMods(const TArray<FAggregatorMod>& InMods, const FAggregatorEvaluateParameters& Parameters)
{
	float Multiplier = 1.0f;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Multiplier *= Mod.EvaluatedMagnitude;
		}
	}

	return Multiplier;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods-gameplaytags"></a>
##### 4.5.4.2 モディファイアのゲームプレイタグ

[Modifier](#concepts-ge-mods)ごとに、`SourceTags`と`TargetTags`を設定することができます。これらは、`GameplayEffect`の[Application Tag requirements](#concepts-ge-tags)と同じ働きをします。つまり、タグは効果が適用されたときにのみ考慮されます。つまり、周期的な無限の効果を持つ場合、最初の効果の適用時にのみタグが考慮され、各周期的な実行時には考慮されません。

`Attribute Based`のモディファイアは、`SourceTagFilter`と `TargetTagFilter`を設定することができます。`Attribute Based`のモディファイアのソースとなるアトリビュートの大きさを決定する際に、これらのフィルターはそのアトリビュートに対する特定のモディファイアを除外するために使用されます。ソースやターゲットがフィルターのタグをすべて持っていないモディファイアは除外されます。

これは詳しく言うと ソースASCとターゲットASCのタグは、`GameplayEffects`によってキャプチャされます。ソースASCのタグは、`GameplayEffectSpec`の作成時にキャプチャされ、ターゲットASCのタグは、エフェクトの実行時にキャプチャされます。無限または持続時間のエフェクトのモディファイアが適用される「資格」があり（つまりそのアグリゲータが資格がある）、それらのフィルタが設定されているかどうかを判断する際に、キャプチャされたタグはフィルタと比較されます。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-stacking"></a>
#### 4.5.5 GameplayEffectsの積み重ね
デフォルトでは、`GameplayEffects`は新しい`GameplayEffectSpec`のインスタンスを適用します。`GameplayEffects`はスタックに設定することができ、`GameplayEffectSpec`の新しいインスタンスが追加される代わりに、現在存在する`GameplayEffectSpec`のスタック数が変更されます。スタックは `Duration` と `Infinite` の `GameplayEffects` でのみ機能します。

スタックには2つのタイプがあります。「ソースによる集約」と「ターゲットによる集約」です。


| Stacking Type       | Description                                                                                                                          |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| Aggregate by Source | ターゲット上のソースの `ASC` ごとに、スタックの個別のインスタンスがあります。各ソースはX個のスタックを適用できます。|
| Aggregate by Target | ソースに関わらず、Target上のスタックのインスタンスは1つだけです。各ソースは、共有スタックの上限までスタックを適用できます。|

スタックには、期限切れ、期間更新、期間リセットのポリシーもあります。これらのスタックには、`GameplayEffect`ブループリントにある便利なホバーツールチップがあります。

サンプルプロジェクトには、`GameplayEffect`スタックの変更をリッスンするカスタムブループリントノードが含まれています。HUD UMG Widget はこれを使用して、プレイヤーが持っているパッシブアーマースタックの量を更新します。この `AsyncTask` は、手動で `EndTask()` が呼ばれるまで永遠に存続しますが、これは UMG Widget の `Destruct` イベントで行います。`AsyncTaskEffectStackChanged.h/cpp`を参照してください。

![Listen for GameplayEffect Stack Change BP Node](https://github.com/dohi/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ga"></a>
#### 4.5.6 Granted Abilites: アビリティの付与
`GameplayEffects`は新しい[GameplayAbilities](#concepts-ga)を`ASC`に付与することができます。アビリティを付与できるのは、`Duration`と`Infinite`の`GameplayEffects`のみです。

一般的な使用例としては、ノックバックや引き寄せからプレイヤーを移動させるなど、他のプレイヤーに何かを強要したい場合が挙げられます。この場合、`GameplayEffect`を適用して、自動的に起動するアビリティ(アビリティが付与されたときに自動的に起動する方法については、[Passive Abilities](#concepts-ga-activating-passive)を参照してください)を付与することで、相手に希望の動作をさせることができます。

デザイナーは、`GameplayEffect`が付与するアビリティの種類、付与するレベル、[bindする入力](#concepts-ga-input)、付与したアビリティの除去ポリシーを選択することができます。

| Removal Policy             | Description                                                                                                                                                                     |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cancel Ability Immediately | 付与されたアビリティは、それを付与した`GameplayEffect`がTargetから取り除かれたときに即座にキャンセルされ、取り除かれます。|
| Remove Ability on End      | 付与されたアビリティは、終了することが許され、その後、対象から取り除かれます。|
| Do Nothing                 | 付与されたアビリティは、付与された`GameplayEffect`をTargetから削除しても影響を受けません。対象者は、後に手動で削除されるまで、その能力を永続的に持ちます。 |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-tags"></a>
#### 4.5.7 Gameplay Effect Tags: ゲームプレイ・エフェクト・タグ
`GameplayEffects`は複数の[GameplayTagContainers](#concepts-gt)を持ちます。デザイナーは各カテゴリーの `Added` と `Removed` の `GameplayTagContainers` を編集し、その結果はコンパイル時に `Combined` の `GameplayTagContainer` に反映されます。`Added`タグは、この`GameplayEffect`が追加する、親が以前に持っていなかった新しいタグです。`Removed`タグは親クラスが持っていて、このサブクラスが持っていないタグです。

| Category                          | Description                                                                                                                                                                                                                                                                                                                                                                        |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Gameplay Effect Asset Tags        | `GameplayEffect`が持っているタグです。これらのタグはそれ自体では何の機能もなく、`GameplayEffect`を説明するという目的のみに使用されます。|
| Granted Tags                      | `GameplayEffect`に付随するタグですが、`GameplayEffect`が適用された`ASC`にも付与されます。これらのタグは、`GameplayEffect`が削除されると`ASC`から削除されます。これは`Duration`と`Infinite`の`GameplayEffects`でのみ機能します。|
| Ongoing Tag Requirements          | これらのタグは適用されると、`GameplayEffect`がオンかオフかを決定します。`GameplayEffect`は、オフであってもまだ適用されています。進行中のタグの要件に失敗して`GameplayEffect`がオフになっていても、その後要件が満たされると、`GameplayEffect`は再びオンになり、その修正が適用されます。これは`Duration`と`Infinite`の`GameplayEffects`でのみ機能します。|
| Application Tag Requirements      | `GameplayEffect`がターゲットに適用できるかどうかを判断する、ターゲットのタグ。これらの要件が満たされていない場合、`GameplayEffect`は適用されません。|
| Remove Gameplay Effects with Tags | これらのタグを持つターゲットの `GameplayEffects` は、この `GameplayEffects` の適用が成功すると、ターゲットから削除されます。|

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-immunity"></a>
#### 4.5.8 Immunity: イミュニティ（免疫・免除）
`GameplayEffects`は[GameplayTags](#concepts-gt)に基づいて、他の`GameplayEffects`の適用を効果的にブロックするイミュニティを付与することができます。イミュニティは `Application Tag Requirements` のような他の手段で効果的に達成することができますが、このシステムを使用すると、イミュニティによって `GameplayEffects` がブロックされたときのためのデリゲートが用意されています `UAbilitySystemComponent::OnImmunityBlockGameplayEffectDelegate`.

`GrantedApplicationImmunityTags`は、ソースの`ASC`(ソースアビリティの`AbilityTags`があればそれも含む)が、指定されたタグのいずれかを持っているかどうかをチェックします。これは、タグに基づいて特定のキャラクターやソースからのすべての`GameplayEffects`からのイミュニティを提供する方法です。

`Application Immunity Query`では、入力された`GameplayEffectSpec`がいずれかのクエリにマッチするかどうかをチェックして、その適用をブロックしたり許可したりします。

これらのクエリは、`GameplayEffect`ブループリントの中で役立つホバーツールチップを備えています。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-spec"></a>
#### 4.5.9 Gameplay Effect Spec
[GameplayEffectSpec](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectSpec/index.html)(`GESpec`)は、`GameplayEffects`のインスタンスと考えることができます。GESpecには、代表する`GameplayEffect`クラスへの参照、どのレベルで作成されたか、誰が作成したかなどの情報が含まれます。デザイナーが事前に作成する必要のある`GameplayEffects`とは異なり、実行時に自由に作成、修正してから適用することができます。`GameplayEffect`を適用する際には、`GameplayEffect`から`GameplayEffectSpec`が作成され、これが実際にターゲットに適用されます。

`GameplayEffectSpecs`は、`BlueprintCallable`である`UAbilitySystemComponent::MakeOutgoingSpec()`を使って`GameplayEffects`から作成されます。`GameplayEffectSpecs`はすぐに適用される必要はありません。アビリティから作成されたプロジェクタイルに`GameplayEffectSpec`を渡して、そのプロジェクタイルが後にヒットするターゲットに適用できるようにするのが一般的です。`GameplayEffectSpec`が正常に適用されると、`FActiveGameplayEffect`という新しい構造体が返されます。

注目すべき`GameplayEffectSpec`の内容。

* この `GameplayEffect` が作成された `GameplayEffect` クラス。
* この`GameplayEffectSpec`のレベル。通常は`GameplayEffectSpec`を作成した能力のレベルと同じですが、異なることもあります。
* `GameplayEffectSpec`の持続時間。デフォルトでは`GameplayEffect`の持続時間と同じですが、異なる場合もあります。
* 周期的な効果のための `GameplayEffectSpec` の期間。デフォルトは `GameplayEffect` の期間ですが、異なっていても構いません。
* この`GameplayEffectSpec`の現在のスタック数。スタックの上限は `GameplayEffect` にあります。
* [GameplayEffectContextHandle](#concepts-ge-context)は、この`GameplayEffectSpec`を作成した人を示します。
* `GameplayEffectSpec` の作成時に、スナップショットによって取得された `Attributes`.
* `GameplayEffectSpec` が、`GameplayEffect` が付与する `GameplayTags` に加えて、ターゲットに付与する `DynamicGrantedTags`。
* `GameplayEffectSpec` が、`GameplayEffect` が持つ `AssetTags` に加えて持つ `DynamicAssetTags` 。
* `SetByCaller` の `TMaps`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-spec-setbycaller"></a>
##### 4.5.9.1 SetByCallers
`SetByCallers`により、`GameplayEffectSpec`は、`GameplayTag`や`FName`に関連付けられたfloatの値を持ち歩くことができます。これらの値はそれぞれの`TMap`に格納されます。`GameplayEffectSpec`の`TMap<FGameplayTag, float>`と`TMap<FName, float>`です。これらは`GameplayEffect`の`Modifier`として、あるいはフロートを移動させるための一般的な手段として使用できます。アビリティの内部で生成された数値データを、`SetByCallers`を介して、[GameplyeEffectExecutionCalculations](#concepts-ge-ec)や[ModifierMagnitudeCalculations](#concepts-ge-mmc)に渡すのが一般的です。

| `SetByCaller` Use | Notes                                                                                                                                                                                                                                                                                                                                                                                                                               |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Modifiers`       | `GameplayEffect`クラスで事前に定義しておく必要があります。`GameplayTag`バージョンのみ使用可能。`GameplayEffect`クラスに定義されていても、`GameplayEffectSpec`に対応するタグとfloat値のペアがない場合、ゲームは`GameplayEffectSpec`の適用時にランタイムエラーを起こし、0を返します。これは`Divide`操作の潜在的な問題です。[Modifiers](#concepts-ge-mods)を参照してください。|
| Elsewhere         | どこかで事前に定義しておく必要はありません。`GameplayEffectSpec`に存在しない`SetByCaller`を読み込むと、開発者が定義したデフォルト値を、オプションの警告とともに返すことができます。|

Blueprintで`SetByCaller`の値を割り当てるには、必要なバージョン（`GameplayTag`または`FName`）のBlueprintノードを使用します。

![Assigning SetByCaller](https://github.com/dohi/GASDocumentation/raw/master/Images/setbycaller.png)

ブループリントで `SetByCaller` の値を読み取るには、ブループリントライブラリにカスタムノードを作成する必要があります。

C++で`SetByCaller`の値を割り当てるには、必要な関数のバージョン(`GameplayTag`または`FName`)を使用します。


```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FName DataName, float Magnitude);
```
```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FGameplayTag DataTag, float Magnitude);
```

C++で`SetByCaller`の値を読むには、必要なバージョンの関数（`GameplayTag`または`FName`）を使います。

```c++
float GetSetByCallerMagnitude(FName DataName, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```
```c++
float GetSetByCallerMagnitude(FGameplayTag DataTag, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```

私は、`FName`バージョンよりも`GameplayTag`バージョンを使用することをお勧めします。これはブループリントのスペルミスを防ぐことができますし、`GameplayTags`は`TMaps`も複製されるため、`FNames`よりも`GameplayEffectSpec`が複製されたときにネットワーク経由で送信する方が効率的です。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-context"></a>
#### 4.5.10 GameplayEffectContext
[GameplayEffectContext](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectContext/index.html)構造体は、`GameplayEffectSpec`の`instigator`と[TargetData](#concepts-targeting-data)に関する情報を保持します。また、[ModifierMagnitudeCalculations](#concepts-ge-mmc) / [GamealleyEffectExecutionCalculations](#concepts-ge-ec)、[AttributeSets](#concepts-as)、[GameplayCues](#concepts-gc)のように、任意のデータを受け渡すためにサブクラス化するのにも適した構造体です。

`GameplayEffectContext`をサブクラス化するには。

1. サブクラス `FGameplayEffectContext` を作成する。
1. オーバーライド `FGameplayEffectContext::GetScriptStruct()`
1. オーバーライド `FgameplayEffectContext::Duplicate()`
1. 新しいデータを複製する必要がある場合は、`FGameplayEffectContext::NetSerialize()`をオーバーライドする。
1. 親構造体である `FGameplayEffectContext` のように、サブクラスに `TStructOpsTypeTraits` を実装する。
1. 自分の[AbilitySystemGlobals](#concepts-asg)クラスの`AllocGameplayEffectContext()`をオーバーライドして、自分のサブクラスの新しいオブジェクトを返す。

[GASShooter](https://github.com/dohi/GASShooter)では、サブクラス化された`GameplayEffectContext`を使って、`GameplayCues`でアクセスできる`TargetData`を追加しています。特にショットガンは複数の敵に当てることができるので、そのために必要です。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mmc"></a>
#### 4.5.11 Modifier Magnitude Calculation (MMC): モディファイアのマグニチュード計算
[ModifierMagnitudeCalculations](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayModMagnitudeCalculation/index.html) (`ModMagCalc`または`MMC`)は、`GameplayEffects`の[Modifiers](#concepts-ge-mod)として使われる強力なクラスです。これらのクラスは、[GameplayEffectExecutionCalculations](#concepts-ge-ec)と似たような機能を持っていますが、より強力というわけではなく、最も重要なのは[predicted](#concepts-p)であるということです。その唯一の目的は、`CalculateBaseMagnitude_Implementation()`からfloatの値を返すことです。ブループリントや C++ では、この関数をサブクラス化してオーバーライドすることができます。

`MMCs` は `Instant`, `Duration`, `Infinite`, `Periodic` といった `GameplayEffects` のどの期間でも使用できます。

`MMC`の強みは、`GameplayEffect`の`Source`または`Target`にある任意の数の`Attributes`の値を取得できることにあり、`GameplayTags`や`SetByCallers`を読み取るために`GameplayEffectSpec`に完全にアクセスできます。`Attributes` はスナップショットされてもされなくても構いません。スナップショットされた`Attributes`は`GameplayEffectSpec`が作成されたときにキャプチャされ、スナップショットされていない`Attributes`は`GameplayEffectSpec`が適用されたときにキャプチャされ、`Infinite`と`Duration`の`GameplayEffects`の`Attribute`が変更されたときに自動的に更新されます。`Attribute`をキャプチャすると、`ASC`上の既存のmodから`CurrentValue`を再計算します。この再計算は、`AbilitySet`内の[PreAttributeChange()](#concepts-as-preattributechange)を実行しないので、クランプはここで再度行う必要があります。

| Snapshot | Source or Target | Captured on `GameplayEffectSpec` | `Infinite`または`Duration`の`GE` が `Attribute` を変更するときに、自動的に更新されるか |
| -------- | ---------------- | -------------------------------- | -------------------------------------------------------------------------------- |
| Yes      | Source           | Creation                         | No                                                                               |
| Yes      | Target           | Application                      | No                                                                               |
| No       | Source           | Application                      | Yes                                                                              |
| No       | Target           | Application                      | Yes                                                                              |

`MMC`から得られる結果の浮動小数点数は、さらに`GameplayEffect`の`Modifier`において、係数と、その前後の係数の加算によって修正することができます。

`MMC`の例としては、`Target`のマナ`Attribute`をキャプチャして、`Target`が持っているマナの量と`Target`が持っている可能性のあるタグに応じて減少量が変化する毒効果からそれを減少させます。
```c++
UPAMMC_PoisonMana::UPAMMC_PoisonMana()
{

	//ManaDef defined in header FGameplayEffectAttributeCaptureDefinition ManaDef;
	ManaDef.AttributeToCapture = UPAAttributeSetBase::GetManaAttribute();
	ManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	ManaDef.bSnapshot = false;

	//MaxManaDef defined in header FGameplayEffectAttributeCaptureDefinition MaxManaDef;
	MaxManaDef.AttributeToCapture = UPAAttributeSetBase::GetMaxManaAttribute();
	MaxManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	MaxManaDef.bSnapshot = false;

	RelevantAttributesToCapture.Add(ManaDef);
	RelevantAttributesToCapture.Add(MaxManaDef);
}

float UPAMMC_PoisonMana::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	// Gather the tags from the source and target as that can affect which buffs should be used
	const FGameplayTagContainer* SourceTags = Spec.CapturedSourceTags.GetAggregatedTags();
	const FGameplayTagContainer* TargetTags = Spec.CapturedTargetTags.GetAggregatedTags();

	FAggregatorEvaluateParameters EvaluationParameters;
	EvaluationParameters.SourceTags = SourceTags;
	EvaluationParameters.TargetTags = TargetTags;

	float Mana = 0.f;
	GetCapturedAttributeMagnitude(ManaDef, Spec, EvaluationParameters, Mana);
	Mana = FMath::Max<float>(Mana, 0.0f);

	float MaxMana = 0.f;
	GetCapturedAttributeMagnitude(MaxManaDef, Spec, EvaluationParameters, MaxMana);
	MaxMana = FMath::Max<float>(MaxMana, 1.0f); // Avoid divide by zero

	float Reduction = -20.0f;
	if (Mana / MaxMana > 0.5f)
	{
		// Double the effect if the target has more than half their mana
		Reduction *= 2;
	}
	
	if (TargetTags->HasTagExact(FGameplayTag::RequestGameplayTag(FName("Status.WeakToPoisonMana"))))
	{
		// Double the effect if the target is weak to PoisonMana
		Reduction *= 2;
	}
	
	return Reduction;
}
```

もし`MMC`の`constructor`の中で`RelevantAttributesToCapture`に`FGamemeveectAtttributeCaptureDefinition`を追加せずに`Attributes`をキャプチャーしようとすると、キャプチャー中にSpecがないというエラーが発生します。もし `Attributes` をキャプチャする必要がないのであれば、`RelevantAttributesToCapture` に何も追加する必要はありません。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ec"></a>
#### 4.5.12 Gameplay Effect Execution Calculation: ゲームプレイ効果の実行計算
[GameplayEffectExecutionCalculations](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectExecutionCalculat-/index.html) (`ExecutionCalculation`, `Execution` (プラグインのソースコードにはこの用語がよく出てきます)、または`ExecCalc`)は、`GameplayEffects`が`ASC`に変更を加える最も強力な方法です。[ModifierMagnitudeCalculations](#concepts-ge-mmc)のように、これらは `Attribute` をキャプチャし、オプションでスナップショットを作成することができます。`MMCs`とは異なり、これらは複数の`Attribute`を変更することができ、基本的にプログラマーが望む他のことを行うことができます。このパワーと柔軟性の欠点は、[predict](#concepts-p)ができないことと、C++で実装しなければならないことです。

`ExecutionCalculations`は`Instant`と`Periodic`の`GameplayEffects`でのみ使用できます。`Execute`という言葉が入っているものは、通常、これらの2種類の`GameplayEffects`を指します。

スナップショットでは、`GameplayEffectSpec`が作成されたときの`Attribute`をキャプチャしますが、スナップショットではない場合、`GameplayEffectSpec`が適用されたときの`Attribute`をキャプチャします。`Attribute`をキャプチャすると、`ASC`上の既存のmodから`CurrentValue`を再計算します。この再計算は、`AbilitySet`内の[PreAttributeChange()](#concepts-as-preattributechange)を実行しないため、クランプはここで再度行う必要があります。

| Snapshot | Source or Target | Captured on `GameplayEffectSpec` |
| -------- | ---------------- | -------------------------------- |
| Yes      | Source           | Creation                         |
| Yes      | Target           | Application                      |
| No       | Source           | Application                      |
| No       | Target           | Application                      |

`Attribute`のキャプチャを設定するには、Epic の ActionRPG サンプル プロジェクトのパターンに従って、`Attribute`を保持する構造体を定義し、`Attribute`のキャプチャ方法を定義して、構造体のコンストラクタでそのコピーを作成します。すべての `ExecCalc` に対して、このような構造体が必要になります。

**注意：** 各構造体は同じ名前空間を共有しているため、固有の名前が必要です。構造体に同じ名前を使用すると、`Attributes`をキャプチャする際に正しくない動作をします（ほとんどの場合、間違った`Attributes`の値をキャプチャします）。

`Local Predicted`, `Server Only`, `Server Initiated` [GameplayAbilities](#concepts-ga)では、`ExecCalc`はサーバ上でのみ呼び出されます。

`Source`と`Target`の多くの`Attribute`から読み取った複雑な計算式に基づいて受けたダメージを計算することは、`ExecCalc`の最も一般的な例です。同梱されているサンプルプロジェクトには、ダメージを計算するためのシンプルな`ExecCalc`があります。これは`GameplayEffectSpec`の[SetByCaller](#concepts-ge-spec-setbycaller)からダメージの値を読み取り、`Target`から取得したアーマーの`Attribute`に基づいてその値を軽減します。`GDDamageExecCalculation.cpp/.h`を参照してください。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ec-senddata"></a>
##### 4.5.12.1 実行計算へのデータ送信
データを `ExecutionCalculation` に送るには、`Attributes` をキャプチャする以外にもいくつかの方法があります。

<a name="concepts-ge-ec-senddata-setbycaller"></a>
###### 4.5.12.1.1 SetByCaller
`GameplayEffectSpec`に設定された任意の[`SetByCallers`](#concepts-ge-spec-setbycaller)は、`ExecutionCalculation`に直接読み込むことができます。

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
float Damage = FMath::Max<float>(Spec.GetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName("Data.Damage")), false, -1.0f), 0.0f);
```

<a name="concepts-ge-ec-senddata-backingdataattribute"></a>
###### 4.5.12.1.2 バッキングデータ属性の計算モディファイア
`GameplayEffect`に値をハードコーディングしたい場合は、キャプチャした`Attribute`の1つをバッキングデータとして使用する`CalculationModifier`を使って値を渡すことができます。

このスクリーンショットの例では、キャプチャしたDamage `Attribute`に50を追加しています。これを `Override` に設定して、ハードコードされた値だけを取り入れることもできます。

![バックデータ属性計算モディファイア](https://github.com/dohi/GASDocumentation/raw/master/Images/calculationmodifierbackingdataattribute.png)

`ExecutionCalculation`は、`Attribute`をキャプチャするときにこの値を読み込みます。

```c++
float Damage = 0.0f;
// Capture optional damage value set on the damage GE as a CalculationModifier under the ExecutionCalculation
ExecutionParams.AttemptCalculateCapturedAttributeMagnitude(DamageStatics().DamageDef, EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-backingdatatempvariable"></a>
###### 4.5.12.1.3 バッキングデータ一時変数計算モディファイア
`GameplayEffect`に値をハードコーディングしたい場合は、`Temporary Variable`やC++でいうところの`Transient Aggregator`を使った`CalculationModifier`を使って値を渡すことができます。`Temporary Variable`は、`GameplayTag`に関連付けられています。

このスクリーンショットの例では、`Data.Damage`の`GameplayTag`を使って、`Temporary Variable`に50を追加しています。

![バッキングデータ一時変数計算修正](https://github.com/dohi/GASDocumentation/raw/master/Images/calculationmodifierbackingdatatempvariable.png)

`ExecutionCalculation`のコンストラクタにバッキング用の`Temporary Variables`を追加します。

```c++
ValidTransientAggregatorIdentifiers.AddTag(FGameplayTag::RequestGameplayTag("Data.Damage"));
```

`ExecutionCalculation`は、`Attribute`のキャプチャ関数と同様の特別なキャプチャ関数を使用して、この値を読み込みます。

```c++
float Damage = 0.0f;
ExecutionParams.AttemptCalculateTransientAggregatorMagnitude(FGameplayTag::RequestGameplayTag("Data.Damage"), EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-effectcontext"></a>
###### 4.5.12.1.4 GameplayEffect Context
カスタムの[GameplayEffectSpecのGameplayEffectContext](#concepts-ge-context)を介して`ExecutionCalculation`にデータを送ることができます。

`ExecutionCalculation`では、`EffectContext`に`FgamemplayEffectCustomExecutionParameters`からアクセスできます。

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(Spec.GetContext().Get());
```

`GameplayEffectSpec`や`EffectContext`を変更する必要がある場合は、以下をご覧ください。

```c++
FGameplayEffectSpec* MutableSpec = ExecutionParams.GetOwningSpecForPreExecuteMod();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(MutableSpec->GetContext().Get());
```

`ExecutionCalculation`内の`GameplayEffectSpec`を変更する場合は注意が必要です。GetOwningSpecForPreExecuteMod()`のコメントを参照してください。

```c++
/** Non const access. Be careful with this, especially when modifying a spec after attribute capture. */
FGameplayEffectSpec* GetOwningSpecForPreExecuteMod() const;
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-car"></a>
#### 4.5.13 カスタムアプリケーション要件
[CustomApplicationRequirement](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectCustomApplication-/index.html) (`CAR`) クラスは、`GameplayEffect` に単純な `GameplayTag` チェックを行うのではなく、`GameplayEffect` を適用できるかどうかについて設計者に高度な制御を提供します。これらはブループリントでは`CanApplyGameplayEffect()`をオーバーライドすることで、C++では`CanApplyGameplayEffect_Implementation()`をオーバーライドすることで実装できます。

`CARs`を使用する場合の例です。

* `Target` は一定量の `Attribute` を持つ必要があります。
* `Target` は `GameplayEffect` のスタックを一定数持っている必要があります。

また、`CARs`は、この`GameplayEffect`のインスタンスがすでに`Target`にあるかどうかをチェックし、新しいインスタンスを適用する代わりに、既存のインスタンスの[持続時間を変更する](#concepts-ge-duration)といった、より高度なことも可能です(`CanApplyGameplayEffect()`でfalseを返す)。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cost"></a>
#### 4.5.14 Cost GameplayEffect
[GameplayAbility](#concepts-ga)には、アビリティのコストとして使用するために特別に設計された、オプションの`GameplayEffect`があります。コストとは、`GameplayAbility`を発動させるために`ASC`がどれだけの`Attribute`を持っている必要があるかということです。もし`GA`が`コストGE`を支払えないのであれば、発動することはできません。この`コストGE`は、`Attribute`から減算する1つ以上の`Modifiers`を持つ`Instant` `GameplayEffect`でなければなりません。デフォルトでは、`Cost GEs`は予測されるものであり、その能力を維持するために`ExecutionCalculations`を使用しないことが推奨されます。つまり、`ExecutionCalculations`は使用しないでください。`MMCs`は完全に受け入れ可能であり、複雑なコスト計算には推奨されます。

最初のうちは、コストを持つ「GA」ごとに1つのユニークな「コストGE」を持つことが多いでしょう。より高度なテクニックとしては、1つの`Cost GE`を複数の`GA`で再利用し、`Cost GE`から作成した`GameplayEffectSpec`を`GA`固有のデータで変更するだけです(コスト値は`GA`で定義されます)。 **これは `Instanced` アビリティにのみ有効です。**

`コストGE`を再利用するには2つの方法があります。

1. **`MMC`を使用する。** これは最も簡単な方法です。[MMC](#concepts-ge-mmc)を作成し、`GameplayEffectSpec`から得られる`GameplayAbility`インスタンスからコスト値を読み込みます。

```c++
float UPGMMC_HeroAbilityCost::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->Cost.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

この例では、コスト値は、私が追加した`GameplayAbility`の子クラスが持つ`FScalableFloat`です。
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cost")
FScalableFloat Cost;
```

![Cost GE With MMC](https://github.com/dohi/GASDocumentation/raw/master/Images/costmmc.png)

2. **オーバーライド `UGameplayAbility::GetCostGameplayEffect()`.** この関数をオーバーライドして、[実行時にGameplayEffectを作成](#concepts-ge-dynamic) し、`GameplayAbility` のコスト値を読み込みます。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cooldown"></a>
#### 4.5.15 Cooldown Gameplay Effect
[GameplayAbilities](#concepts-ga)には、アビリティのクールダウンとして使用するために特別に設計された、オプションの`GameplayEffect`があります。クールダウンとは、アビリティが発動してからどれくらいの期間で、再び発動できるかを決めるものです。もし`GA`がまだクールダウン中であれば、それは起動できません。この`Cooldown GE`は`Duration`の`GameplayEffect`であり、`Modifiers`はなく、`GameplayAbility`ごと、あるいは（クールダウンを共有するスロットに割り当てられた交換可能なアビリティがあるゲームの場合）アビリティスロットごとにユニークな`GameplayTag`を`GameplayEffect's`GrantedTags`（「`Cooldown Tag`」）に記述する必要があります。GA` は実際には `Cooldown GE` の存在ではなく、`Cooldown Tag` の存在をチェックします。デフォルトでは、`クールダウン GE` は予測されることを意味しており、`ExecutionCalculations` を使用しないという意味で、その能力を維持することが推奨されます。複雑なクールダウン計算を行う場合には、`MMCs`は全く問題なく、推奨されます。

最初のうちは、クールダウンを持つ`GA`ごとに、固有の`Cooldown GE`を1つ持つことが多いでしょう。より高度なテクニックとしては、複数の`GA`に対して1つの`Cooldown GE`を再利用し、`Cooldown GE`から作成した`GameplayEffectSpec`を`GA`固有のデータ（クールダウン時間と`Cooldown Tag`は`GA`で定義されます）で変更するだけです。**これは `Instanced` のアビリティにのみ有効です**。

`Cooldown GE`を再利用するには2つの方法があります。

1. **[SetByCaller](#concepts-ge-spec-setbycaller)を使用する** これが最も簡単な方法です。共有している`Cooldown GE`の持続時間を`GameplayTag`で`SetByCaller`に設定します。あなたの`GameplayAbility`サブクラス上で、持続時間のためのfloat / `FScalableFloat`、ユニークな`Cooldown Tag`のための`FGameplayTagContainer`、そして私たちの`Cooldown Tag`と`Cooldown GE's`タグの結合のリターン・ポインタとして使用する一時的な`FGameplayTagContainer`を定義します。

```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// Temp container that we will return the pointer to in GetCooldownTags().
// This will be a union of our CooldownTags and the Cooldown GE's cooldown tags.
UPROPERTY()
FGameplayTagContainer TempCooldownTags;
```

そして、`UGameplayAbility::GetCooldownTags()`をオーバーライドして、私たちの`Cooldown Tags`と既存の`Cooldown GE's`タグの結合を返します。
```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

最後に、`UGameplayAbility::ApplyCooldown()`をオーバーライドして、`Cooldown Tags`を注入し、`SetByCaller`をcooldownの`GameplayEffectSpec`に追加します。

```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		SpecHandle.Data.Get()->SetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName(  OurSetByCallerTag  )), CooldownDuration.GetValueAtLevel(GetAbilityLevel()));
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

この図では、クールダウンの持続時間の `Modifier` が `SetByCaller` に設定されており、`Data Tag` が `Data.Cooldown` になっています。上のコードでは、`Data.Cooldown`は`OurSetByCallerTag`になります。

![Cooldown GE with SetByCaller](https://github.com/dohi/GASDocumentation/raw/master/Images/cooldownsbc.png)

2. **[MMC](#concepts-ge-mmc)を使用する** これは、`Cooldown GE`と`ApplyCooldown`において、`SetByCaller`を継続時間として設定することを除いて、上記と同じ設定を行います。代わりに、持続時間を `Custom Calculation Class` に設定し、これから作成する新しい `MMC` を指定します。
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// Temp container that we will return the pointer to in GetCooldownTags().
// This will be a union of our CooldownTags and the Cooldown GE's cooldown tags.
UPROPERTY()
FGameplayTagContainer TempCooldownTags;
```

そして、`UGameplayAbility::GetCooldownTags()`をオーバーライドして、私たちの`Cooldown Tags`と既存の`Cooldown GE's`タグの結合を返します。
```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

最後に、`UGameplayAbility::ApplyCooldown()`をオーバーライドして、クールダウンの`GameplayEffectSpec`に`Cooldown Tags`を注入します。
```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

```c++
float UPGMMC_HeroAbilityCooldown::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->CooldownDuration.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

![Cooldown GE with MMC](https://github.com/dohi/GASDocumentation/raw/master/Images/cooldownmmc.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cooldown-tr"></a>
##### 4.5.15.1 Cooldown Gameplay Effectの残り時間の取得
```c++
bool APGPlayerState::GetCooldownRemainingForTag(FGameplayTagContainer CooldownTags, float & TimeRemaining, float & CooldownDuration)
{
	if (AbilitySystemComponent && CooldownTags.Num() > 0)
	{
		TimeRemaining = 0.f;
		CooldownDuration = 0.f;

		FGameplayEffectQuery const Query = FGameplayEffectQuery::MakeQuery_MatchAnyOwningTags(CooldownTags);
		TArray< TPair<float, float> > DurationAndTimeRemaining = AbilitySystemComponent->GetActiveEffectsTimeRemainingAndDuration(Query);
		if (DurationAndTimeRemaining.Num() > 0)
		{
			int32 BestIdx = 0;
			float LongestTime = DurationAndTimeRemaining[0].Key;
			for (int32 Idx = 1; Idx < DurationAndTimeRemaining.Num(); ++Idx)
			{
				if (DurationAndTimeRemaining[Idx].Key > LongestTime)
				{
					LongestTime = DurationAndTimeRemaining[Idx].Key;
					BestIdx = Idx;
				}
			}

			TimeRemaining = DurationAndTimeRemaining[BestIdx].Key;
			CooldownDuration = DurationAndTimeRemaining[BestIdx].Value;

			return true;
		}
	}

	return false;
}
```

**注意：** クライアントのクールダウンの残り時間を問い合わせるには、そのクライアントが複製された`GameplayEffects`を受信できることが必要です。これは、クライアントの`ASC`の[レプリケーションモード](#concepts-asc-rm)に依存します。

<a name="concepts-ge-cooldown-listen"></a>
##### 4.5.15.2 クールダウンの開始と終了のリスニング
クールダウンが開始されたときにリスンするには、`AbilitySystemComponent->OnActiveGamelyEffectAddedDelegateToSelf`にバインドして`Cooldown GE`が適用されたときに反応するか、`AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)`にバインドして`Cooldown Tag`が追加されたときに反応するかのどちらかになります。`Cooldown GE`が追加された時には、それを適用した`GameplayEffectSpec`にもアクセスできるので、こちらでリスニングすることをお勧めします。これにより、`Cooldown GE`がローカルで予測されたものなのか、サーバーが修正したものなのかを判断することができます。

クールダウンが終了したことを知るには、`AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate()`にバインドして`Cooldown GE`が削除されたときに反応するか、`AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)`にバインドして`Cooldown Tag`が削除されたときに反応するかのどちらかになります。サーバーで修正された`Cooldown GE`が入ってくると、ローカルで予測されたものが削除され、まだクールダウン中であるにもかかわらず、「OnAnyGameplayEffectRemovedDelegate()」が発火することになるので、`Cooldown Tag`が削除されたときを待つことをお勧めします。予測された`Cooldown GE`が削除され、サーバー側で修正された`Cooldown GE`が適用される間、`Cooldown Tag`は変化しません。

**注意：** クライアントで `GameplayEffect` の追加や削除をリッスンするには、クライアントが複製された `GameplayEffects` を受信できる必要があります。これはクライアントの`ASC`の[replication mode](#concepts-asc-rm)に依存します。

サンプル プロジェクトには、クールダウンの開始と終了をリッスンするカスタム ブループリント ノードが含まれています。HUD UMG Widget はこれを使用して、Meteor のクールダウンの残り時間を更新します。この `AsyncTask` は、手動で `EndTask()` が呼ばれるまで永遠に存続しますが、これは UMG Widget の `Destruct` イベントで行います。AsyncTaskEffectCooldownChanged.h/cpp`を参照してください。

![クールダウンの変更を聞く BP ノード](https://github.com/dohi/GASDocumentation/raw/master/Images/cooldownchange.png)

<a name="concepts-ge-cooldown-prediction"></a>
##### 4.5.15.3 クールダウンの予測
現在、クールダウンを予測することはできません。ローカルに予測された`Cooldown GE`が適用されたときにUIのクールダウン・タイマーを開始することはできますが、`GameplayAbility`の実際のクールダウンは、サーバーのクールダウンの残り時間と連動しています。プレイヤーのレイテンシーによっては、ローカルに予測されたクールダウンが終了しても、サーバー上では`GameplayAbility`がクールダウンされたままになっていることがあり、この場合、サーバーのクールダウンが終了するまで、`GameplayAbility`がすぐに再アクティブになることはありません。

サンプルプロジェクトでは、ローカルで予測されたクールダウンが始まるとMeteorアビリティのUIアイコンをグレーアウトさせ、サーバーの修正された`Cooldown GE`が入ってくるとクールダウンタイマーを開始することで、この問題を処理しています。

この結果、ゲーム上では、レイテンシーの高いプレイヤーは、レイテンシーの低いプレイヤーに比べてクールダウンの短いアビリティの発火率が低くなり、不利な状況に陥ります。Fortniteでは、武器にクールダウン`GameplayEffects`を使用しないカスタムブックキーピングを採用することでこれを回避しています。

本当の意味で予測されたクールダウンを可能にすること（プレイヤーは、ローカルのクールダウンが終了しても、サーバーがまだクールダウン中であれば、`GameplayAbility`を起動することができます）は、Epicが[GASの将来のイテレーション](#concepts-p-future)でいつか実装したいと考えていることです。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-duration"></a>
#### 4.5.16 アクティブなGameplayEffectの持続時間の変更
`Cooldown GE`や任意の`Duration` `GameplayEffect`の残り時間を変更するには、`GameplayEffectSpec`の`Duration`を変更し、`StartServerWorldTime`を更新し、`CachedStartServerWorldTime`を更新し、`StartWorldTime`を更新し、`CheckDuration()`で継続時間のチェックを再実行する必要があります。これをサーバー上で行い、`FActiveGameplayEffect`をダーティにすることで、クライアントに変更が反映されます。
**注意：** これは `const_cast` を伴うもので、Epic が意図した持続時間の変更方法ではないかもしれませんが、今のところうまく機能しているようです。

```c++
bool UPAAbilitySystemComponent::SetGameplayEffectDurationHandle(FActiveGameplayEffectHandle Handle, float NewDuration)
{
	if (!Handle.IsValid())
	{
		return false;
	}

	const FActiveGameplayEffect* ActiveGameplayEffect = GetActiveGameplayEffect(Handle);
	if (!ActiveGameplayEffect)
	{
		return false;
	}

	FActiveGameplayEffect* AGE = const_cast<FActiveGameplayEffect*>(ActiveGameplayEffect);
	if (NewDuration > 0)
	{
		AGE->Spec.Duration = NewDuration;
	}
	else
	{
		AGE->Spec.Duration = 0.01f;
	}

	AGE->StartServerWorldTime = ActiveGameplayEffects.GetServerWorldTime();
	AGE->CachedStartServerWorldTime = AGE->StartServerWorldTime;
	AGE->StartWorldTime = ActiveGameplayEffects.GetWorldTime();
	ActiveGameplayEffects.MarkItemDirty(*AGE);
	ActiveGameplayEffects.CheckDuration(Handle);

	AGE->EventSet.OnTimeChanged.Broadcast(AGE->Handle, AGE->StartWorldTime, AGE->GetDuration());
	OnGameplayEffectDurationChange(*AGE);

	return true;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-dynamic"></a>
#### 4.5.17 ランタイムにダイナミックなGameplayEffectsを作成する
ランタイムにダイナミックな`GameplayEffects`を作成することは、高度なトピックです。あまり頻繁に実行する必要はありません。

C++でスクラッチから実行時に作成できるのは`Instant`の`GameplayEffects`のみです。`Duration`と`Infinite`の`GameplayEffects`は、複製する際に存在しない`GameplayEffect`のクラス定義を探すため、実行時に動的に作成することはできません。この機能を実現するためには、通常のエディタで行うように、代わりにアーキタイプの`GameplayEffect`クラスを作成する必要があります。そして、実行時に必要なもので`GameplayEffectSpec`のインスタンスをカスタマイズします。

ランタイムに作成された`Instant` `GameplayEffects`は、[local predicted](#concepts-p) `GameplayAbility`の中から呼び出すこともできます。ただし、動的に作成されたものが副作用をもたらすかどうかはまだ不明です。

##### 例

サンプルプロジェクトでは、キャラクターが必殺の一撃を受けたときに、ゴールドと経験値を犯人に送り返すものを`AttributeSet`で作成しています。

```c++
// Create a dynamic instant Gameplay Effect to give the bounties
UGameplayEffect* GEBounty = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Bounty")));
GEBounty->DurationPolicy = EGameplayEffectDurationType::Instant;

int32 Idx = GEBounty->Modifiers.Num();
GEBounty->Modifiers.SetNum(Idx + 2);

FGameplayModifierInfo& InfoXP = GEBounty->Modifiers[Idx];
InfoXP.ModifierMagnitude = FScalableFloat(GetXPBounty());
InfoXP.ModifierOp = EGameplayModOp::Additive;
InfoXP.Attribute = UGDAttributeSetBase::GetXPAttribute();

FGameplayModifierInfo& InfoGold = GEBounty->Modifiers[Idx + 1];
InfoGold.ModifierMagnitude = FScalableFloat(GetGoldBounty());
InfoGold.ModifierOp = EGameplayModOp::Additive;
InfoGold.Attribute = UGDAttributeSetBase::GetGoldAttribute();

Source->ApplyGameplayEffectToSelf(GEBounty, 1.0f, Source->MakeEffectContext());
```

2つ目の例では、ローカルに予測される`GameplayAbility`の中に作成されたランタイムの`GameplayEffect`を示しています。ご自身の責任でお使いください（コードのコメントを参照）。

```c++
UGameplayAbilityRuntimeGE::UGameplayAbilityRuntimeGE()
{
	NetExecutionPolicy = EGameplayAbilityNetExecutionPolicy::LocalPredicted;
}

void UGameplayAbilityRuntimeGE::ActivateAbility(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo, const FGameplayEventData* TriggerEventData)
{
	if (HasAuthorityOrPredictionKey(ActorInfo, &ActivationInfo))
	{
		if (!CommitAbility(Handle, ActorInfo, ActivationInfo))
		{
			EndAbility(Handle, ActorInfo, ActivationInfo, true, true);
		}

		// Create the GE at runtime.
		UGameplayEffect* GameplayEffect = NewObject<UGameplayEffect>(GetTransientPackage(), TEXT("RuntimeInstantGE"));
		GameplayEffect->DurationPolicy = EGameplayEffectDurationType::Instant; // Only instant works with runtime GE.

		// Add a simple scalable float modifier, which overrides MyAttribute with 42.
		// In real world applications, consume information passed via TriggerEventData.
		const int32 Idx = GameplayEffect->Modifiers.Num();
		GameplayEffect->Modifiers.SetNum(Idx + 1);
		FGameplayModifierInfo& ModifierInfo = GameplayEffect->Modifiers[Idx];
		ModifierInfo.Attribute.SetUProperty(UMyAttributeSet::GetMyModifiedAttribute());
		ModifierInfo.ModifierMagnitude = FScalableFloat(42.f);
		ModifierInfo.ModifierOp = EGameplayModOp::Override;

		// Apply the GE.

		// Create the GESpec here to avoid the behavior of ASC to create GESpecs from the GE class default object.
		// Since we have a dynamic GE here, this would create a GESpec with the base GameplayEffect class, so we
		// would lose our modifiers. Attention: It is unknown, if this "hack" done here can have drawbacks!
		// The spec prevents the GE object being collected by the GarbageCollector, since the GE is a UPROPERTY on the spec.
		FGameplayEffectSpec* GESpec = new FGameplayEffectSpec(GameplayEffect, {}, 0.f); // "new", since lifetime is managed by a shared ptr within the handle
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, FGameplayEffectSpecHandle(GESpec));
	}
	EndAbility(Handle, ActorInfo, ActivationInfo, false, false);
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-containers"></a>
#### 4.5.18 ゲームプレイエフェクトコンテナ
Epicの[Action RPG Sample Project](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)では、`FGameplayEffectContainer`という構造を実装しています。これはバニラGASにはありませんが、`GameplayEffects`や[TargetData](#concepts-targeting-data)を格納するのに非常に便利です。また、`GameplayEffects`から`GameplayEffectSpecs`を作成したり、`GameplayEffectContext`にデフォルト値を設定するなどの作業を自動化することができます。`GameplayEffectContainer`を`GameplayAbility`の中に作り、それをスポーンされたプロジェクタイルに渡すことはとても簡単で分かりやすいです。同梱されているサンプル・プロジェクトでは、`GameplayEffectContainer`を実装していませんが、GASを使っているプロジェクトでは、ぜひ`GameplayEffectContainer`の実装を検討してみてください。

`GameplayEffectContainers`内の`GESpecs`にアクセスして、`SetByCallers`を追加するなどの操作を行うには、`FGameplayEffectContainer`をブレークして、`GESpecs`の配列内のインデックスで`GESpec`のリファレンスにアクセスします。これには、アクセスしたい`GESpec`のインデックスを前もって知っておく必要があります。

![SetByCaller with a GameplayEffectContainer](https://github.com/dohi/GASDocumentation/raw/master/Images/gecontainersetbycaller.png)

`GameplayEffectContainer`には、オプションで効率的な[Targeting](#concepts-targeting-containers)の手段も含まれています。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga"></a>
### 4.6 Gameplay Abilities

<a name="concepts-ga-definition"></a>
#### 4.6.1 ゲームプレイアビリティの定義
[`GameplayAbilities`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/UGameplayAbility/index.html) (`GA`)とは、ゲーム内で `Actor` ができるアクションやスキルのことです。例えば、スプリントしながら銃を撃つなど、一度に複数の`GameplayAbility`を有効にすることができます。これらはブループリントやC++で作ることができます。

`GameplayAbility`の例です。

* ジャンプ
* スプリント
* 銃を撃つ
* X秒ごとに攻撃をパッシブにブロックする
* ポーションの使用
* 扉を開く
* リソースの収集
* 建物を建設する

`GameplayAbilities`で実装してはいけないもの

* 基本的な移動入力
* UIとのいくつかのインタラクション - お店でアイテムを購入するのに`GameplayAbility`を使ってはいけません。

これらはルールではなく、私が推奨しているだけであり、あなたのデザインや実装方法によって異なる場合があります。

`GameplayAbility`にはデフォルトで、属性の変更量を調整するためのレベルや、`GameplayAbility`の機能を変更するためのレベルが用意されています。

`GameplayAbility`は[`Net Execution Policy`](#concepts-ga-net)に応じて、所有するクライアントやサーバー上で実行されますが、シミュレートされたプロキシは使用できません。`Net Execution Policy`は`GameplayAbility`がローカルに[予測](#concepts-p)されるかどうかを決定します。これらは[オプションのコストとクールダウンのGameplayEffects](#concepts-ga-commit)のデフォルトの動作を含みます。`GameplayAbilities`では、イベントを待ったり、属性変更を待ったり、プレイヤーがターゲットを選ぶのを待ったり、`Root Motion Source`で`Character`を動かしたりと、時間をかけて行われるアクションには[`AbilityTasks`](#concepts-at)を使用します。**シミュレートされたクライアントは、`GameplayAbility`を実行しません。** 代わりに、サーバーがアビリティを実行すると、シミュレートされたプロキシ上で視覚的に再生する必要があるもの(アニメーションのモンタージュなど)は、`AbilityTasks`や、サウンドやパーティクルなどの化粧品のための[`GameplayCues`](#concepts-gc)を通じて複製またはRPCされます。

すべての`GameplayAbility`は、その`ActivateAbility()`関数をゲームプレイのロジックでオーバーライドします。EndAbility()には、GameplayAbilityが完了したときやキャンセルされたときに実行されるロジックを追加することができます。

シンプルな`GameplayAbility`のフローチャートです。
![Simple GameplayAbility Flowchart](https://github.com/dohi/GASDocumentation/raw/master/Images/abilityflowchartsimple.png)

より複雑な`GameplayAbility`のフローチャート。
![Complex GameplayAbility Flowchart](https://github.com/dohi/GASDocumentation/raw/master/Images/abilityflowchartcomplex.png)

複雑なアビリティは、複数の`GameplayAbility`を使って実装することができ、それらは相互に作用（起動、キャンセルなど）します。

<a name="concepts-ga-definition-reppolicy"></a>
##### 4.6.1.1 レプリケーション・ポリシー
このオプションは使わないでください。誤解を招く名前ですし、必要ありません。[`GameplayAbilitySpecs`](#concepts-ga-spec)は、デフォルトではサーバーから所有するクライアントにレプリケートされます。前述のように、**`GameplayAbility`はシミュレーションされたプロキシ上では動作しません。** `GameplayAbilities`は、`AbilityTasks`と`GameplayCues`を使用して、シミュレートされたプロキシへの視覚的な変更をレプリケートまたはRPCします。EpicのDave Ratti氏は、[将来的にこのオプションを削除したい](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)と述べています。

<a name="concepts-ga-definition-remotecancel"></a>
##### 4.6.1.2 Server Respects Remote Ability Cancellation（サーバーはリモートアビリティキャンセルを尊重する）
このオプションはトラブルの原因になることが多いです。つまり、クライアントの`GameplayAbility`がキャンセルや自然な完了によって終了した場合、完了したかどうかに関わらず、サーバーのバージョンを強制的に終了させるということです。後者の問題が重要で、特にレイテンシーの高いプレイヤーが使用するローカルに予測される`GameplayAbility`の場合は注意が必要です。一般的には、このオプションを無効にすることをお勧めします。

<a name="concepts-ga-definition-repinputdirectly"></a>
##### 4.6.1.3 Replicate Input Directly
このオプションを設定すると、入力のプレスおよびリリースイベントが常にサーバーに複製されます。これは、既存の入力関連の[`AbilityTasks`](#concepts-at)に組み込まれている`Generic Replicated Events`で、[input bound to your ASC](#concepts-ga-input)がある場合に使用することをお勧めします。

エピックのコメント
```c++
/** 直接的な入力状態のレプリケーションです。これらは、アビリティのbReplicateInputDirectlyがtrueの場合に呼び出され、一般的には使用しない方が良いでしょう。(代わりにGeneric Replicated Eventsを使用することをお勧めします）。) */
UAbilitySystemComponent::ServerSetInputPressed()
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-input"></a>
#### 4.6.2 ASCへの入力のバインド
ASCには入力アクションを直接バインドすることができ、それらの入力を付与する際に`GameplayAbilities`に割り当てることができます。`GameplayAbilities`に割り当てられた入力アクションは、`GameplayTag`の要件が満たされている場合、押されると自動的にそれらの`GameplayAbilities`を起動します。割り当てられた入力アクションは、入力に反応する内蔵の`AbilityTasks`を使用するために必要です。

`GameplayAbilities`の起動に割り当てられた入力アクションに加えて、`ASC`は一般的な`Confirm`と`Cancel`の入力も受け付けます。これらの特別な入力は、`AbilityTasks`が[`Target Actors`](#concepts-targeting-actors)などの確認やキャンセルに使用します。

入力を `ASC` にバインドするには、まず入力アクション名をバイトに変換する列挙体を作成する必要があります。列挙型の名前は、プロジェクトの設定でInput Actionに使われている名前と正確に一致する必要があります。`DisplayName`は重要ではありません。

サンプルプロジェクトより：
```c++
UENUM(BlueprintType)
enum class EGDAbilityInputID : uint8
{
	// 0 None
	None			UMETA(DisplayName = "None"),
	// 1 Confirm
	Confirm			UMETA(DisplayName = "Confirm"),
	// 2 Cancel
	Cancel			UMETA(DisplayName = "Cancel"),
	// 3 LMB
	Ability1		UMETA(DisplayName = "Ability1"),
	// 4 RMB
	Ability2		UMETA(DisplayName = "Ability2"),
	// 5 Q
	Ability3		UMETA(DisplayName = "Ability3"),
	// 6 E
	Ability4		UMETA(DisplayName = "Ability4"),
	// 7 R
	Ability5		UMETA(DisplayName = "Ability5"),
	// 8 Sprint
	Sprint			UMETA(DisplayName = "Sprint"),
	// 9 Jump
	Jump			UMETA(DisplayName = "Jump")
};
```

もし、`Character`の上に`ASC`があるのであれば、`SetupPlayerInputComponent()`の中に`ASC`にバインドするための関数を入れてください。
```c++
// Bind to AbilitySystemComponent
AbilitySystemComponent->BindAbilityActivationToInputComponent(PlayerInputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"), FString("CancelTarget"), FString("EGDAbilityInputID"), static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

もしあなたの`ASC`が`PlayerState`上に存在するならば、`SetupPlayerInputComponent()`の内部では、`PlayerState`がまだクライアントにレプリケートされていないかもしれないという潜在的な競合状態があります。そのため、`SetupPlayerInputComponent()`と`OnRep_PlayerState()`の両方で入力へのバインドを試みることをお勧めします。プレイヤーコントローラがクライアントに `ClientRestart()` を呼び出して `InputComponent` を作成する前に `PlayerState` がレプリケートされると、`Actor` の `InputComponent` が NULL になってしまう可能性があるため、`OnRep_PlayerState()` だけでは十分ではありません。サンプルプロジェクトでは、実際に入力を一度だけバインドするようにプロセスをゲートするブール値を使用して、両方の場所にバインドしようとしています。

**注意：** サンプルプロジェクトでは、列挙された`Confirm`と`Cancel`がプロジェクト設定の入力アクション名（`ConfirmTarget`と`CancelTarget`）と一致していませんが、`BindAbilityActivationToInputComponent()`でそれらのマッピングを提供しています。これらはマッピングを提供しているため特別なもので、一致する必要はありませんが、一致させることは可能です。列挙された他の入力は、プロジェクト設定の入力アクション名と一致する必要があります。

1つの入力でしか起動しない（MOBAのように常に同じ「スロット」に存在する）`GameplayAbility`については、`UGameplayAbility`のサブクラスに変数を追加して、入力を定義するのが良いでしょう。そして、アビリティを付与する際に、`ClassDefaultObject`からこれを読み取ることができます。

<a name="concepts-ga-input-noactivate"></a>
##### 4.6.2.1 アビリティを起動させずに入力にバインドする
入力が押されたときに`GameplayAbility`を自動的に起動したくないが、`AbilityTasks`で使用するために入力にバインドしたい場合は、`UGameplayAbility`のサブクラスに`bActivateOnInput`という新しいbool変数を追加して、デフォルトを`true`とし、`UAbilitySystemComponent::AbilityLocalInputPressed()`をオーバーライドすることができます。

```c++
void UGSAbilitySystemComponent::AbilityLocalInputPressed(int32 InputID)
{
	// Consume the input if this InputID is overloaded with GenericConfirm/Cancel and the GenericConfim/Cancel callback is bound
	if (IsGenericConfirmInputBound(InputID))
	{
		LocalInputConfirm();
		return;
	}

	if (IsGenericCancelInputBound(InputID))
	{
		LocalInputCancel();
		return;
	}

	// ---------------------------------------------------------

	ABILITYLIST_SCOPE_LOCK();
	for (FGameplayAbilitySpec& Spec : ActivatableAbilities.Items)
	{
		if (Spec.InputID == InputID)
		{
			if (Spec.Ability)
			{
				Spec.InputPressed = true;
				if (Spec.IsActive())
				{
					if (Spec.Ability->bReplicateInputDirectly && IsOwnerActorAuthoritative() == false)
					{
						ServerSetInputPressed(Spec.Handle);
					}

					AbilitySpecInputPressed(Spec);

					// Invoke the InputPressed event. This is not replicated here. If someone is listening, they may replicate the InputPressed event to the server.
					InvokeReplicatedEvent(EAbilityGenericReplicatedEvent::InputPressed, Spec.Handle, Spec.ActivationInfo.GetActivationPredictionKey());
				}
				else
				{
					UGSGameplayAbility* GA = Cast<UGSGameplayAbility>(Spec.Ability);
					if (GA && GA->bActivateOnInput)
					{
						// Ability is not active, so try to activate it
						TryActivateAbility(Spec.Handle);
					}
				}
			}
		}
	}
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-granting"></a>
#### 4.6.3 アビリティの付与
`GameplayAbility`を`ASC`に付与すると、`ASC`の`ActivatableAbilities`リストに追加され、[`GameplayTag` requirements](#concepts-ga-tags)を満たせば、その`GameplayAbility`を自由に起動できるようになります。

`GameplayAbilities`をサーバーに付与すると、サーバーは自動的に[`GameplayAbilitySpec`](#concepts-ga-spec)を所有するクライアントに複製します。他のクライアントやシミュレートされたプロキシは`GameplayAbilitySpec`を受け取りません。

サンプルプロジェクトでは、`Character`クラスに`TArray<TSubclassOf<UGDGameplayAbility>>`を格納し、ゲームの開始時に読み込んで付与します。
```c++
void AGDCharacterBase::AddCharacterAbilities()
{
	// Grant abilities, but only on the server	
	if (Role != ROLE_Authority || !AbilitySystemComponent.IsValid() || AbilitySystemComponent->CharacterAbilitiesGiven)
	{
		return;
	}

	for (TSubclassOf<UGDGameplayAbility>& StartupAbility : CharacterAbilities)
	{
		AbilitySystemComponent->GiveAbility(
			FGameplayAbilitySpec(StartupAbility, GetAbilityLevel(StartupAbility.GetDefaultObject()->AbilityID), static_cast<int32>(StartupAbility.GetDefaultObject()->AbilityInputID), this));
	}

	AbilitySystemComponent->CharacterAbilitiesGiven = true;
}
```

これらの`GameplayAbility`を付与する際には、`UGameplayAbility`クラス、アビリティレベル、バインドされている入力、`SourceObject`つまり誰がこの`GameplayAbility`をこの`ASC`に与えたか、などを含む`GameplayAbilitySpecs`を作成しています。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-activating"></a>
#### 4.6.4 アビリティの有効化
`GameplayAbility`に入力アクションが割り当てられている場合、入力が押されて、その`GameplayTag`の要件を満たしていれば、自動的に起動します。これは必ずしも望ましい方法ではありません。ASCでは、`GameplayTag`、`GameplayAbility`クラス、`GameplayAbilitySpec`ハンドル、イベントの4つの方法で`GameplayAbility`を有効にすることができます。イベントによって`GameplayAbility`を有効にすると、[イベントでデータのペイロードを渡す](#concepts-ga-data)ことができます。

```c++
UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilitiesByTag(const FGameplayTagContainer& GameplayTagContainer, bool bAllowRemoteActivation = true);

UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilityByClass(TSubclassOf<UGameplayAbility> InAbilityToActivate, bool bAllowRemoteActivation = true);

bool TryActivateAbility(FGameplayAbilitySpecHandle AbilityToActivate, bool bAllowRemoteActivation = true);

bool TriggerAbilityFromGameplayEvent(FGameplayAbilitySpecHandle AbilityToTrigger, FGameplayAbilityActorInfo* ActorInfo, FGameplayTag Tag, const FGameplayEventData* Payload, UAbilitySystemComponent& Component);

FGameplayAbilitySpecHandle GiveAbilityAndActivateOnce(const FGameplayAbilitySpec& AbilitySpec);
```

イベントによって`GameplayAbility`を起動するには、`GameplayAbility`に`Triggers`が設定されている必要があります。`GameplayTag`を割り当てて、`GameplayEvent`のオプションを選択する。イベントを送信するには、関数`UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`を使用します。イベントで`GameplayAbility`をアクティブにすることで、データの入ったペイロードを渡すことができます。

また、`GameplayAbility`の`Triggers`では、`GameplayTag`が追加または削除されたときに`GameplayAbility`を有効にすることができます。

**注意：** ブループリントでイベントから`GameplayAbility`をアクティブにする場合、`ActivateAbilityFromEvent`ノードを使用しなければならず、標準の`ActivateAbility`ノードは **グラフ内に存在させることはできません**。標準の `ActivateAbility` ノードがグラフ内に存在しなければ、`ActivateAbilityFromEvent` ノードが常に呼び出されます。

**注意：** 常にパッシブアビリティのように動作する`GameplayAbility`を持たせない限り、`GameplayAbility`が終了するときに`EndAbility()`を呼び出すことを忘れないでください。

**ローカルに予測される** `GameplayAbility` の起動順序。
1. **Owning client** が`TryActivateAbility()`を呼び出す
1. `InternalTryActivateAbility()`を呼び出す。
1. `CanActivateAbility()`を呼び出し、`GameplayTag`の要件が満たされているかどうか、`ASC`がコストを負担できるかどうか、`GameplayAbility`がクールダウンしていないかどうか、他のインスタンスが現在アクティブでないかどうかを返す。
1. `CallServerTryActivateAbility()`を呼び出し、生成した`Prediction Key`を渡す
1. `CallActivateAbility()`を呼び出す。
1. `PreActivate()` を呼び出す。Epic はこれを "boilerplate init stuff（ボイラープレートを使った初期設定）" と呼んでいます。
1. `ActivateAbility()` を呼び出して、最終的に能力を有効にします。

**サーバー** が `CallServerTryActivateAbility()` を受け取る
1. `ServerTryActivateAbility()`を呼び出す。
1. `InternalServerTryActivateAbility()`を呼び出す。
1. `InternalTryActivateAbility()`を呼び出す。
1. `CanActivateAbility()`を呼び出し、`GameplayTag`の要件が満たされているか、`ASC`にコストが出せるか、`GameplayAbility`がクールダウンしていないか、他のインスタンスが現在アクティブでないかを返す。
1. 成功した場合は、`ClientActivateAbilitySucceed()`を呼び出し、起動がサーバーによって確認されたことを`ActivationInfo`に更新するように指示し、`OnConfirmDelegate`デリゲートをブロードキャストする。これは、入力確認とは異なります。
1. `CallActivateAbility()`を呼び出す。
1. `PreActivate()` を呼び出す。 Epic はこれを "boilerplate init stuff（ボイラープレートを使った初期設定）" と呼んでいます。
1. `ActivivateAbility()`を呼び出して、最終的に能力を起動させます。

サーバーが起動に失敗した場合は、`ClientActivateAbilityFailed()`が呼び出され、クライアントの`GameplayAbility`が直ちに終了し、予測された変更が取り消されます。

<a name="concepts-ga-activating-passive"></a>
##### 4.6.4.1 パッシブ・アビリティ
自動的に起動して継続的に動作するパッシブな`GameplayAbility`を実装するには、`GameplayAbility`が付与されて`AvatarActor`が設定されたときに自動的に呼ばれる`UGameplayAbility::OnAvatarSet()`をオーバーライドして、`TryActivateAbility()`を呼び出します。

カスタムの`UGameplayAbility`クラスには、付与されたときに`GameplayAbility`を有効にするかどうかを指定する`bool`を追加することをお勧めします。サンプルプロジェクトでは、パッシブ・アーマー・スタッキング・アビリティにこのような設定をしています。

パッシブな`GameplayAbility`は通常、[`Net Execution Policy`](#concepts-ga-net)が`Server Only`となっています。

```c++
void UGDGameplayAbility::OnAvatarSet(const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilitySpec & Spec)
{
	Super::OnAvatarSet(ActorInfo, Spec);

	if (ActivateAbilityOnGranted)
	{
		bool ActivatedAbility = ActorInfo->AbilitySystemComponent->TryActivateAbility(Spec.Handle, false);
	}
}
```

Epicでは、この機能を、パッシブ アビリティを開始したり、`BeginPlay`タイプの処理を行うのに適した場所としています。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-cancelabilities"></a>
#### 4.6.5 アビリティのキャンセル
内部で`GameplayAbility`をキャンセルするには、`CancelAbility()`を呼び出します。これにより、`EndAbility()`が呼び出され、`WasCancelled`パラメータがtrueに設定されます。

外部から`GameplayAbility`をキャンセルするには、`ASC`がいくつかの関数を提供します。

```c++
/** Cancels the specified ability CDO. */
void CancelAbility(UGameplayAbility* Ability);	

/** Cancels the ability indicated by passed in spec handle. If handle is not found among reactivated abilities nothing happens. */
void CancelAbilityHandle(const FGameplayAbilitySpecHandle& AbilityHandle);

/** Cancel all abilities with the specified tags. Will not cancel the Ignore instance */
void CancelAbilities(const FGameplayTagContainer* WithTags=nullptr, const FGameplayTagContainer* WithoutTags=nullptr, UGameplayAbility* Ignore=nullptr);

/** Cancels all abilities regardless of tags. Will not cancel the ignore instance */
void CancelAllAbilities(UGameplayAbility* Ignore=nullptr);

/** Cancels all abilities and kills any remaining instanced abilities */
virtual void DestroyActiveState();
```

**注意：** `CancelAllAbilities`は、`Non-Instanced` `GameplayAbility`があるとうまく動作しないことがわかりました。`Non-Instanced` `GameplayAbility`にヒットした際に、あきらめてしまうようです。`CancelAbilities`は、`Non-Instanced` `GameplayAbilities`をうまく扱うことができるため、サンプルプロジェクトではこれを使用しています(Jumpは `Non-Instanced` `GameplayAbility`です)。あなたの好みに合わせて調整してください。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-definition-activeability"></a>
#### 4.6.6 アクティブアビリティの取得
初心者の方はよく 「`アクティブなアビリティ`を取得するにはどうしたらいいですか」 と質問されますが、これはたぶん、アビリティに変数を設定したり、キャンセルするためでしょう。一度に複数の`GameplayAbility`がアクティブになることがあるので、`アクティブなアビリティ` は一つではありません。その代わりに、`ASC`の`ActivatableAbilities`（`ASC`が所有する付与された`GameplayAbilities`）のリストを検索して、探している[`Asset`または`Granted`の`GameplayTag`](#concepts-ga-tags)に一致するものを見つける必要があります。

`UAbilitySystemComponent::GetActivatableAbilities()`は、`TArray<FGameplayAbilitySpec>`を返すので、繰り返し処理することができます。

また、`ASC`には、`GameplayTagContainer`をパラメータとして受け取る別のヘルパー関数があり、手作業で`GameplayAbilitySpecs`のリストを繰り返し検索する代わりに、検索を支援します。`bOnlyAbilitiesThatSatisfyTagRequirements`パラメータは、その`GameplayTag`の要件を満たし、今すぐにでも有効化できる`GameplayAbilitySpecs`のみを返します。例えば、武器を使ったものと素手の拳を使ったもの、2つの基本攻撃の`GameplayAbility`があり、`GameplayTag`の要件を設定している武器が装備されているかどうかによって、正しい方が発動するような場合です。詳しくは、この関数に関するEpicのコメントを参照してください。
```c++
UAbilitySystemComponent::GetActivatableGameplayAbilitySpecsByAllMatchingTags(const FGameplayTagContainer& GameplayTagContainer, TArray < struct FGameplayAbilitySpec* >& MatchingGameplayAbilities, bool bOnlyAbilitiesThatSatisfyTagRequirements = true)
```

探している`FGameplayAbilitySpec`を入手したら、それに対して`IsActive()`を呼び出すことができます。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-instancing"></a>
#### 4.6.7 Instancing Policy: インスタンス化の方針
`GameplayAbility`の`Instancing Policy`は、その `GameplayAbility`がアクティブになったときに、どのようにインスタンス化されるかを決定します。

| `Instancing Policy`     | Description                                                                                      | Example of when to use                                                                                                                                                                                                                                                                                                                                                                                             |
| ----------------------- | ------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Instanced Per Actor     | 各`ASC`には1つだけ`GameplayAbility`のインスタンスがあり、アクティベーションの間に再利用されます。|これはおそらく、最も使用する `Instancing Policy` になるでしょう。どのようなアビリティにも使用することができ、アクティベーション間の永続性を提供します。アクティベーション間で必要な変数を手動でリセットするのはデザイナーの責任です。|
| Instanced Per Execution |`GameplayAbility`をアクティブにするたびに、新しいインスタンスが作成されます。|`GameplayAbility`の利点は、起動するたびに変数がリセットされることです。これらは起動するたびに新しい`GameplayAbility`が生成されるので、`Instanced Per Actor`よりもパフォーマンスが悪くなります。サンプルプロジェクトでは、これらは一切使用していません。|
| Non-Instanced           |`GameplayAbility`は、その`ClassDefaultObject`を操作するものです。インスタンスは作成されません。|3つの中で最もパフォーマンスが高いですが、使用できる内容が最も制限されています。Non-Instancedな`GameplayAbility`は状態を保存できません。つまり、ダイナミック変数や、`AbilityTask`デリゲートへのバインドはできません。MOBAやRTSでミニオンの基本攻撃のように頻繁に使用される単純なアビリティに使用するのが最適です。サンプルプロジェクトのジャンプの`GameplayAbility`は`Non-Instanced`です。|

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-net"></a>
#### 4.6.8 Net Execution Policy: ネット実行ポリシー
`GameplayAbility`の `Net Execution Policy` は、誰がどのような順番で `GameplayAbility`を実行するかを決定します。

| `Net Execution Policy` | Description                                                                                                                                                                                                         |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Local Only`           |`GameplayAbility`は所有するクライアント上でのみ実行されます。これは、ローカルな化粧品の変更のみを行うアビリティに便利です。シングルプレイヤーゲームでは、`Server Only`を使用してください。|
| `Local Predicted`      |`Local Predicted` `GameplayAbilities`は、まず所有するクライアントで起動し、次にサーバーで起動します。サーバーのバージョンでは、クライアントの予測が間違っていたものを修正します。[予測](#concepts-p)を参照してください。|
| `Server Only`          |`GameplayAbility`はサーバー上でのみ実行されます。パッシブな`GameplayAbility`は通常、`Server Only`となります。シングルプレイヤーゲームではこれを使用してください。|
| `Server Initiated`     |`Server Initiated` `GameplayAbilities`は、まずサーバーで起動し、次に所有するクライアントで起動します。個人的にはあまり使ったことはありませんが。| 

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-tags"></a>
#### 4.6.9 アビリティタグ
`GameplayAbilities`には、ビルトインのロジックが組み込まれた `GameplayTagContainers` が付属しています。これらの`GameplayTags`はどれも複製されません。

| `GameplayTag Container`     | Description                                                                                                                                                                                   |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Ability Tags`              | `GameplayAbility`が所有する`GameplayTags`です。これらは`GameplayAbility`を説明するための`GameplayTags`に過ぎません。|
| `Cancel Abilities with Tag` | これらの`GameplayTags`を`Ability Tags`に持つ`GameplayAbilities`は、この`GameplayAbility`が起動するとキャンセルされます。 |
| `Block Abilities with Tag`  | これらの`GameplayTags`を`Ability Tags`に持つ`GameplayAbilities`は、この`GameplayAbility`がアクティブである間は発動できません。 |
| `Activation Owned Tags`     | これらの`GameplayTags`は、`GameplayAbility`がアクティブである間、`GameplayAbility`のオーナーに与えられます。これらのタグは複製されません。|
| `Activation Required Tags`  | オーナーがこれらの `GameplayTags` を**すべて**持っている場合にのみ、この`GameplayAbility` は有効になります。 |
| `Activation Blocked Tags`   | オーナーがこれらの`GameplayTags`の**どれか**を持っている場合、この`GameplayAbility`は起動できません。 |
| `Source Required Tags`      | この`GameplayAbility`は、`Source`がこれらの`GameplayTags`の**すべて**を持っている場合にのみ有効になります。`Source`の`GameplayTags`は、イベントによって`GameplayAbility`がトリガーされた場合にのみ設定されます。 |
| `Source Blocked Tags`       | この`GameplayAbility`は、`Source`がこれらの`GameplayTags`の**どれか**を持っている場合には起動できません。`Source`の`GameplayTags`は、イベントによって`GameplayAbility`がトリガーされた場合にのみ設定されます。 |
| `Target Required Tags`      | この`GameplayAbility`は、`Target`がこれらの`GameplayTags`を**すべて**持っている場合にのみ有効になります。ターゲットの`GameplayTags`は、イベントによって`GameplayAbility`がトリガーされた場合にのみ設定されます。 |
| `Target Blocked Tags`       | この`GameplayAbility`は、`Target`がこれらの`GameplayTags`の**どれか**を持っている場合には起動できません。ターゲットの`GameplayTags`は、イベントによって`GameplayAbility`がトリガーされた場合にのみ設定されます。 |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-spec"></a>
#### 4.6.10 Gameplay Ability Spec
`GameplayAbilitySpec`は`GameplayAbility`が付与された後に`ASC`上に存在し、起動可能な`GameplayAbility`を定義します。`GameplayAbility`のクラス、レベル、入力バインディング、実行時の状態は`GameplayAbility`のクラスとは別に保持しなければなりません。

サーバで`GameplayAbility`が付与されると、サーバは所有するクライアントに`GameplayAbilitySpec`を複製し、クライアントがそれを有効にできるようにします。

`GameplayAbilitySpec`を有効にすると、その`Instancing Policy`に応じて`GameplayAbility`のインスタンスが生成されます（`Non-Instanced` `GameplayAbilities`の場合は生成されません）。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-data"></a>
#### 4.6.11 アビリティへのデータの渡し方
`GameplayAbilities`の一般的なパラダイムは、`Activate->Generate Data->Apply->End`です。しかし、時には既存のデータに基づいて行動する必要があります。GASには、外部データを`GameplayAbilities`に取り込むためのオプションがいくつか用意されています。

| Method                                          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ----------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| イベントで`GameplayAbility`をアクティベートする             | データのペイロードを含むイベントで`GameplayAbility`を起動します。イベントのペイロードはクライアントからサーバーに複製され、ローカルに予測される `GameplayAbility` に適用されます。既存の変数に当てはまらない任意のデータには、2つの`Optional Object`または[`TargetData`](#concepts-targeting-data)変数を使用します。この方法の欠点は、インプットバインドでアビリティを起動できなくなることです。イベントで`GameplayAbility`を起動するには、`GameplayAbility`に`Triggers`を設定しておく必要があります。`GameplayTag`を割り当てて、`GameplayEvent`のオプションを選択します。イベントを送信するには、関数`UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`を使用します。 |
| `WaitGameplayEvent` `AbilityTask` を使用する          | `WaitGameplayEvent` `AbilityTask`を使って、`GameplayAbility`がアクティブになった後、ペイロードデータを持つイベントをリッスンするように指示します。イベントのペイロードとそれを送信する処理は、イベントによって`GameplayAbility`をアクティベートするのと同じです。イベントは`AbilityTask`では複製されないので、`Local Only`や`Server Only`の`GameplayAbilities`にのみ使用する必要があるという欠点があります。イベントのペイロードを複製する独自の`AbilityTask`を書くこともできます。 |
| `TargetData` を使用する                               | カスタムの`TargetData`構造体は、クライアントとサーバーの間で任意のデータを渡すのに適した方法です。 |
| `OwnerActor` または `AvatarActor` にデータを保存する | `OwnerActor`や`AvatarActor`など、参照できるオブジェクトに格納されている複製された変数を使います。この方法は最も柔軟性が高く、インプットバインドで起動された`GameplayAbilities`でも動作します。ただし、使用時にデータがレプリケーションから同期されていることは保証されません。つまり、レプリケートされた変数を設定した後、すぐに`GameplayAbility`を起動しても、パケットロスの可能性があるため、受信側で起こる順序は保証されません。 |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-commit"></a>
#### 4.6.12 アビリティのコストとクールダウン
`GameplayAbility`にはオプションでコストとクールダウンの機能があります。コストとは、即効性のある`GameplayEffect`（[`Cost GE`](#concepts-ge-cost)）で実装されている`GameplayAbility`を起動するために`ASC`が持たなければならない定義済みの`Attributes`の量です。クールダウンは、期限が切れるまで`GameplayAbility`の再起動を防ぐタイマーで、`Duration` `GameplayEffect` です([`Cooldown GE`](#concepts-ge-cooldown) で実装されます。)

`GameplayAbility`が`UGameplayAbility::Activate()`を呼び出す前に、`UGameplayAbility::CanActivateAbility()`を呼び出します。この関数は所有している`ASC`がコストを支払えるかどうかをチェックし(`UGameplayAbility::CheckCost()`)、`GameplayAbility`がクールダウンしていないかどうかを確認します(`UGameplayAbility::CheckCooldown()`)。

`GameplayAbility`が`Activate()`を呼び出した後、`UGameplayAbility::CommitAbility()`を使って、コストとクールダウンをいつでも任意にコミットすることができます。これは`UGameplayAbility::CommitCost()`と`UGameplayAbility::CommitCooldown()`を呼び出します。デザイナーは`CommitCost()`や`CommitCooldown()`を同時にコミットしてはいけない場合、別々に呼び出すこともできます。コストとクールダウンをコミットすると、`CheckCost()`と`CheckCooldown()`がもう一度呼び出され、それらに関連する`GameplayAbility`が失敗する最後のチャンスとなります。所有する `ASC` の `Attributes` は、`GameplayAbility` が有効になった後に変更される可能性があり、コミット時のコストを満たすことができません。コミット時に[予測キー](#concepts-p-key)が有効であれば、コストとクールダウンを[ローカルに予測](#concepts-p)することができます。

実装の詳細については、[`CostGE`](#concepts-ge-cost)と[`CooldownGE`](#concepts-ge-cooldown)を参照してください。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-leveling"></a>
#### 4.6.13 アビリティのレベルアップ
アビリティのレベルアップには、一般的に2つの方法があります。

| Level Up Method                            | Description                                                                                                                                                                                                      |
| ------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Ungrant and Regrant at the New Level       | ASCから`GameplayAbility`をUngrant(削除)して、サーバーの次のレベルでRegrantします。これにより、その時点で`GameplayAbility`が有効であった場合は終了します。 |
| Increase the `GameplayAbilitySpec's` Level | サーバー上で`GameplayAbilitySpec`を見つけ、そのレベルを上げ、ダーティにマークして、所有するクライアントに複製します。このメソッドは、`GameplayAbility`がその時点でアクティブであった場合、それを終了しません。 |

この2つの方法の主な違いは、レベルアップ時にアクティブな`GameplayAbilities`をキャンセルさせたいかどうかです。使用している`GameplayAbility`に応じて両方の方法を使うことになるでしょう。どちらの方法を使用するかは、`UGameplayAbility`のサブクラスに`bool`を追加することをお勧めします。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-sets"></a>
#### 4.6.14 アビリティセット
`GameplayAbilitySets`は便利な`UDataAsset`クラスで、キャラクターが起動する`GameplayAbilities`の入力バインディングとリストを保持し、`GameplayAbilities`を付与するロジックを持ちます。また、サブクラスには追加のロジックやプロパティを含めることができます。Paragonではヒーローごとに、与えられたすべての`GameplayAbilities`を含む`GameplayAbilitySet`を持っていました。

少なくとも私がこれまで見てきた中では、このクラスは不要だと思います。サンプルプロジェクトでは、`GDCharacterBase`とそのサブクラスの中で、`GameplayAbilitySets`のすべての機能を処理しています。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-batching"></a>
#### 4.6.15 アビリティのバッチ処理
従来の`GameplayAbility`のライフサイクルには、クライアントからサーバーへの最低2～3回のRPCが含まれています。

1. `CallServerTryActivateAbility()`
1. `ServerSetReplicatedTargetData()` (オプション)
1. `ServerEndAbility()`

`GameplayAbility`がこれらのアクションをフレーム内で1つのアトミックなグループにまとめて実行する場合、このワークフローを最適化して、2つまたは3つのRPCのすべてを1つのRPCにバッチ（結合）することができます。`GAS`ではこのRPCの最適化を`Ability Batching`と呼んでいます。`Ability Batching`を使用する一般的な例としては、ヒットスキャン・ガンがあります。ヒットスキャン・ガンは起動し、ライントレースを行い、[`TargetData`](#concepts-targeting-data)をサーバに送信し、1フレーム内の1つのアトミックグループでアビリティを終了します。[GASShooter](https://github.com/dohi/GASShooter)サンプルプロジェクトでは、ヒットスキャン・ガンでこのテクニックを実演しています。

セミオートマチック銃は最良のシナリオであり、`CallServerTryActivateAbility()`、`ServerSetReplicatedTargetData()`（弾のヒット結果）、`ServerEndAbility()`を3つのRPCではなく1つのRPCにまとめます。

フルオート/バーストガンでは、最初の弾の`CallServerTryActivateAbility()`と`ServerSetReplicatedTargetData()`を、2つのRPCではなく1つのRPCにまとめます。後続の各弾は、独自の`ServenSetReplicatedTargetData()`RPCです。最後に、銃の発射が止まると、`ServerEndAbility()`が別のRPCとして送られます。これは最悪のケースで、初弾のRPCを2つではなく1つだけに節約するというものです。このシナリオは、[`Gameplay Event`](#concepts-ga-data)を介してアビリティを有効にすることでも実装できました。この場合、弾の`TargetData`が`EventPayload`と共にクライアントからサーバーに送信されます。後者の方法の欠点は、`TargetData`をアビリティの外部で生成しなければならないことです。一方、batchingアプローチは`TargetData`をアビリティの内部で生成します。

`Ability Batching`は、[`ASC`](#concepts-asc)ではデフォルトでは無効になっています。`Ability Batching`を有効にするには、`ShouldDoServerAbilityRPCBatch()`をオーバーライドしてtrueを返します。

```c++
virtual bool ShouldDoServerAbilityRPCBatch() const override { return true; }
```

`Ability Batching`を有効にしたら、バッチ処理したいアビリティを起動する前に、あらかじめ`FScopedServerAbilityRPCBatcher`構造体を作成しておく必要があります。この特殊な構造体は、そのスコープ内でそれに続くアビリティをバッチ処理しようとします。`FScopedServerAbilityRPCBatcher`がスコープから外れると、起動されたアビリティはバッチ化を試みません。`FScopedServerAbilityRPCBatcher`は、バッチ処理が可能な各関数内に、RPCを送信する際のコールを遮断し、代わりにメッセージをバッチ構造体にパックする特別なコードを持つことで機能します。`FScopedServerAbilityRPCBatcher`がスコープから外れると、`UAbilitySystemComponent::EndServerAbilityRPCBatch()`で、このバッチ構造体をサーバに自動的にRPCします。サーバは`UAbilitySystemComponent::ServerAbilityRPCBatch_Internal(FServerAbilityRPCBatch& BatchInfo)`でバッチRPCを受け取ります。`BatchInfo`パラメータには、アビリティを終了すべきかどうか、起動時に入力が押されたかどうかのフラグと、それが含まれていれば`TargetData`が含まれます。この関数は、バッチ処理が適切に行われているかどうかを確認するために、ブレークポイントを置くのに適しています。あるいは、cvar `AbilitySystem.ServerRPCBatching.Log 1` を使って、特別な能力のバッチングのロギングを有効にすることもできます。

このメカニズムはC++でのみ行うことができ、`FGameplayAbilitySpecHandle`によってのみアビリティを起動することができます。

```c++
bool UGSAbilitySystemComponent::BatchRPCTryActivateAbility(FGameplayAbilitySpecHandle InAbilityHandle, bool EndAbilityImmediately)
{
	bool AbilityActivated = false;
	if (InAbilityHandle.IsValid())
	{
		FScopedServerAbilityRPCBatcher GSAbilityRPCBatcher(this, InAbilityHandle);
		AbilityActivated = TryActivateAbility(InAbilityHandle, true);

		if (EndAbilityImmediately)
		{
			FGameplayAbilitySpec* AbilitySpec = FindAbilitySpecFromHandle(InAbilityHandle);
			if (AbilitySpec)
			{
				UGSGameplayAbility* GSAbility = Cast<UGSGameplayAbility>(AbilitySpec->GetPrimaryInstance());
				GSAbility->ExternalEndAbility();
			}
		}

		return AbilityActivated;
	}

	return AbilityActivated;
}
```

GASShooterでは、セミオートマチック銃とフルオートマチック銃に同じバッチ処理された`GameplayAbility`を再利用していますが、これが直接`EndAbility()`を呼び出すことはありません（アビリティの外では、プレイヤーの入力と現在の発射モードに基づくバッチ処理されたアビリティの呼び出しを管理するローカルオンリーのアビリティによって処理されます）。すべてのRPCは、`FScopedServerAbilityRPCBatcher`のスコープ内で発生しなければならないので、制御/管理するローカルオンリーが、このアビリティが`EndAbility()`の呼び出しをバッチ処理する(セミオート用)か、`EndAbility()`の呼び出しをバッチ処理しない(フルオート用)かを指定できるように、`EndAbilityImmediately`パラメータを提供し、`EndAbility()`の呼び出しは、独自のRPCで後から発生するようにしています。

GASShooter は、アビリティのバッチ処理を可能にする Blueprint ノードを公開しており、前述のローカルオンリーのアビリティは、バッチ処理されたアビリティを起動するのに使用します。

![バッチ式能力の起動](https://github.com/dohi/GASDocumentation/raw/master/Images/batchabilityactivate.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-netsecuritypolicy"></a>
#### 4.6.15 NetSecurityPolicy: ネットセキュリティポリシー
`GameplayAbility`の`NetSecurityPolicy`は、ネットワーク上のどこでアビリティを実行するかを決定します。これは、制限されたアビリティを実行しようとするクライアントからの保護を提供します。

| `NetSecurityPolicy`     | Description                                                                                                                                        |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ClientOrServer`        | セキュリティ要件なし。クライアントやサーバーは、このアビリティの実行や終了を自由に引き起こすことができる。 |
| `ServerOnlyExecution`   | この能力の実行を要求するクライアントは、サーバーによって無視されます。クライアントは、サーバーにこの能力の取り消しや終了を要求することができます。 |
| `ServerOnlyTermination` | この能力のキャンセルまたは終了を要求するクライアントは、サーバーによって無視されます。クライアントは能力の実行を要求できます。 |
| `ServerOnly`            | サーバーは、この能力の実行と終了の両方を制御します。クライアントが何らかの要求をしても無視されます。 |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at"></a>
### 4.7 Ability Tasks

<a name="concepts-at-definition"></a>
### 4.7.1 Ability Task Definition
`GameplayAbilities` only execute in one frame. This does not allow for much flexibility on its own. To do actions that happen over time or require responding to delegates fired at some point later in time we use latent actions called `AbilityTasks`.

GAS comes with many `AbilityTasks` out of the box:
* Tasks for moving Characters with `RootMotionSource`
* A task for playing animation montages
* Tasks for responding to `Attribute` changes
* Tasks for responding to `GameplayEffect` changes
* Tasks for responding to player input
* and more

The `UAbilityTask` constructor enforces a hardcoded game-wide maximum of 1000 concurrent `AbilityTasks` running at the same time. Keep this in mind when designing `GameplayAbilities` for games that can have hundreds of characters in the world at the same time like RTS games.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-definition"></a>
### 4.7.2 Custom Ability Tasks
Often you will be creating your own custom `AbilityTasks` (in C++). The Sample Project comes with two custom `AbilityTasks`:
1. `PlayMontageAndWaitForEvent` is a combination of the default `PlayMontageAndWait` and `WaitGameplayEvent` `AbilityTasks`. This allows animation montages to send gameplay events from `AnimNotifies` back to the `GameplayAbility` that started them. Use this to trigger actions at specific times during animation montages.
1. `WaitReceiveDamage` listens for the `OwnerActor` to receive damage. The passive armor stacks `GameplayAbility` removes a stack of armor when the hero receives an instance of damage.

`AbilityTasks` are composed of:
* A static function that creates new instances of the `AbilityTask`
* Delegates that are broadcasted on when the `AbilityTask` completes its purpose
* An `Activate()` function to start its main job, bind to external delegates, etc.
* An `OnDestroy()` function for cleanup, including external delegates that it bound to
* Callback functions for any external delegates that it bound to
* Member variables and any internal helper functions

**Note:** `AbilityTasks` can only declare one type of output delegate. All of your output delegates must be of this type, regardless if they use the parameters or not. Pass default values for unused delegate parameters.

`AbilityTasks` only run on the Client or Server that is running the owning `GameplayAbility`; however, `AbilityTasks` can be set to run on simulated clients by setting `bSimulatedTask = true;` in the `AbilityTask` constructor, overriding `virtual void InitSimulatedTask(UGameplayTasksComponent& InGameplayTasksComponent);`, and setting any member variables to be replicated. This is only useful in rare situations like movement `AbilityTasks` where you don't want to replicate every movement change but instead simulate the entire movement `AbilityTask`. All of the `RootMotionSource` `AbilityTasks` do this. See `AbilityTask_MoveToLocation.h/.cpp` as an example.

`AbilityTasks` can `Tick` if you set `bTickingTask = true;` in the `AbilityTask` constructor and override `virtual void TickTask(float DeltaTime);`. This is useful when you need to lerp values smoothly across frames. See `AbilityTask_MoveToLocation.h/.cpp` as an example.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-using"></a>
### 4.7.3 Using Ability Tasks
To create and activate an `AbilityTask` in C++ (From `GDGA_FireGun.cpp`):
```c++
UGDAT_PlayMontageAndWaitForEvent* Task = UGDAT_PlayMontageAndWaitForEvent::PlayMontageAndWaitForEvent(this, NAME_None, MontageToPlay, FGameplayTagContainer(), 1.0f, NAME_None, false, 1.0f);
Task->OnBlendOut.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnCompleted.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnInterrupted.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->OnCancelled.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->EventReceived.AddDynamic(this, &UGDGA_FireGun::EventReceived);
Task->ReadyForActivation();
```

In Blueprint, we just use the Blueprint node that we create for the `AbilityTask`. We don't have to call `ReadyForActivate()`. That is automatically called by `Engine/Source/Editor/GameplayTasksEditor/Private/K2Node_LatentGameplayTaskCall.cpp`. `K2Node_LatentGameplayTaskCall` also automatically calls `BeginSpawningActor()` and `FinishSpawningActor()` if they exist in your `AbilityTask` class (see `AbilityTask_WaitTargetData`). To reiterate, `K2Node_LatentGameplayTaskCall` only does automagic sorcery for Blueprint. In C++, we have to manually call `ReadyForActivation()`, `BeginSpawningActor()`, and `FinishSpawningActor()`.

![Blueprint WaitTargetData AbilityTask](https://github.com/dohi/GASDocumentation/raw/master/Images/abilitytask.png)

To manually cancel an `AbilityTask`, just call `EndTask()` on the `AbilityTask` object in Blueprint (called `Async Task Proxy`) or in C++.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-rms"></a>
### 4.7.4 Root Motion Source Ability Tasks
GAS comes with `AbilityTasks` for moving `Characters` over time for things like knockbacks, complex jumps, pulls, and dashes using `Root Motion Sources` hooked into the `CharacterMovementComponent`.

**Note:** Predicting `RootMotionSource` `AbilityTasks` works up to engine version 4.19 and 4.25+. Prediction is bugged for engine versions 4.20-4.24; however, the `AbilityTasks` still perform their function in multiplayer with minor net corrections and work perfectly in single player. It is possible to cherry pick the [prediction fix](https://github.com/EpicGames/UnrealEngine/commit/94107438dd9f490e7b743f8e13da46927051bf33#diff-65f6196f9f28f560f95bd578e07e290c) from 4.25 into a custom 4.20-4.24 engine.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc"></a>
### 4.8 Gameplay Cues

<a name="concepts-gc-definition"></a>
#### 4.8.1 Gameplay Cue Definition
`GameplayCues` (`GC`) execute non-gameplay related things like sound effects, particle effects, camera shakes, etc. `GameplayCues` are typically replicated (unless explicitly `Executed`, `Added`, or `Removed` locally) and predicted.

We trigger `GameplayCues` by sending a corresponding `GameplayTag` with the **mandatory parent name of `GameplayCue.`** and an event type (`Execute`, `Add`, or `Remove`) to the `GameplayCueManager` via the `ASC`. `GameplayCueNotify` objects and other `Actors` that implement the `IGameplayCueInterface` can subscribe to these events based on the `GameplayCue's` `GameplayTag` (`GameplayCueTag`).

**Note:** Just to reiterate, `GameplayCue` `GameplayTags` need to start with the parent `GameplayTag` of `GameplayCue`. So for example, a valid `GameplayCue` `GameplayTag` might be `GameplayCue.A.B.C`.

There are two classes of `GameplayCueNotifies`, `Static` and `Actor`. They respond to different events and different types of `GameplayEffects` can trigger them. Override the corresponding event with your logic.

| `GameplayCue` Class                                                                                                                  | Event             | `GameplayEffect` Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ------------------------------------------------------------------------------------------------------------------------------------ | ----------------- | ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`GameplayCueNotify_Static`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayCueNotify_Static/index.html) | `Execute`         | `Instant` or `Periodic`  | Static `GameplayCueNotifies` operate on the `ClassDefaultObject` (meaning no instances) and are perfect for one-off effects like hit impacts.                                                                                                                                                                                                                                                                                                                                                                        |
| [`GameplayCueNotify_Actor`](https://docs.unrealengine.com/en-US/BlueprintAPI/GameplayCueNotify/index.html)                           | `Add` or `Remove` | `Duration` or `Infinite` | Actor `GameplayCueNotifies` spawn a new instance when `Added`. Because these are instanced, they can do actions over time until they are `Removed`. These are good for looping sounds and particle effects that will be removed when the backing `Duration` or `Infinite` `GameplayEffect` is removed or by manually calling remove. These also come with options to manage how many are allowed to be `Added` at the same so that multiple applications of the same effect only start the sounds or particles once. |

`GameplayCueNotifies` technically can respond to any of the events but this is typically how we use them.

**Note:** When using `GameplayCueNotify_Actor`, check `Auto Destroy on Remove` otherwise subsequent calls to `Add` that `GameplayCueTag` won't work.

When using an `ASC` [Replication Mode](#concepts-asc-rm) other than `Full`, `Add` and `Remove` `GC` events will fire twice on Server players (listen server) - once for applying the `GE` and again from the "Minimal" `NetMultiCast` to the clients. However, `WhileActive` events will still only fire once. All events will only fire once on clients.

The Sample Project includes a `GameplayCueNotify_Actor` for stun and sprint effects. It also has a `GameplayCueNotify_Static` for the FireGun's projectile impact. These `GCs` can be optimized further by [triggering them locally](#concepts-gc-local) instead of replicating them through a `GE`. I opted for showing the beginner way of using them in the Sample Project.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-trigger"></a>
#### 4.8.2 Triggering Gameplay Cues

From inside of a `GameplayEffect` when it is successfully applied (not blocked by tags or immunity), fill in the `GameplayTags` of all the `GameplayCues` that should be triggered.

![GameplayCue Triggered from a GameplayEffect](https://github.com/dohi/GASDocumentation/raw/master/Images/gcfromge.png)

`UGameplayAbility` offers Blueprint nodes to `Execute`, `Add`, or `Remove` `GameplayCues`.

![GameplayCue Triggered from a GameplayAbility](https://github.com/dohi/GASDocumentation/raw/master/Images/gcfromga.png)

In C++, you can call functions directly on the `ASC` (or expose them to Blueprint in your `ASC` subclass):

```c++
/** GameplayCues can also come on their own. These take an optional effect context to pass through hit result, etc */
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** Add a persistent gameplay cue */
void AddGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void AddGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** Remove a persistent gameplay cue */
void RemoveGameplayCue(const FGameplayTag GameplayCueTag);
	
/** Removes any GameplayCue added on its own, i.e. not as part of a GameplayEffect. */
void RemoveAllGameplayCues();
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-local"></a>
#### 4.8.3 Local Gameplay Cues
The exposed functions for firing `GameplayCues` from `GameplayAbilities` and the `ASC` are replicated by default. Each `GameplayCue` event is a multicast RPC. This can cause a lot of RPCs. GAS also enforces a maximum of two of the same `GameplayCue` RPCs per net update. We avoid this by using local `GameplayCues` where we can. Local `GameplayCues` only `Execute`, `Add`, or `Remove` on the individual client.

Scenarios where we can use local `GameplayCues`:
* Projectile impacts
* Melee collision impacts
* `GameplayCues` fired from animation montages

Local `GameplayCue` functions that you should add to your `ASC` subclass:

```c++
UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);
```

```c++
void UPAAbilitySystemComponent::ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Executed, GameplayCueParameters);
}

void UPAAbilitySystemComponent::AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::OnActive, GameplayCueParameters);
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::WhileActive, GameplayCueParameters);
}

void UPAAbilitySystemComponent::RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Removed, GameplayCueParameters);
}
```

If a `GameplayCue` was `Added` locally, it should be `Removed` locally. If it was `Added` via replication, it should be `Removed` via replication.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-parameters"></a>
#### 4.8.4 Gameplay Cue Parameters
`GameplayCues` receive a `FGameplayCueParameters` structure containing extra information for the `GameplayCue` as a parameter. If you manually trigger the `GameplayCue` from a function on the `GameplayAbility` or the `ASC`, then you must manually fill in the `GameplayCueParameters` structure that is passed to the `GameplayCue`. If the `GameplayCue` is triggered by a `GameplayEffect`, then the following variables are automatically filled in on the `GameplayCueParameters` structure:

* AggregatedSourceTags
* AggregatedTargetTags
* GameplayEffectLevel
* AbilityLevel
* [EffectContext](#concepts-ge-context)
* Magnitude (if the `GameplayEffect` has an `Attribute` for magnitude selected in the dropdown above the `GameplayCue` tag container and a corresponding `Modifier` that affects that `Attribute`)

The `SourceObject` variable in the `GameplayCueParameters` structure is potentially a good place to pass arbitrary data to the `GameplayCue` when triggering the `GameplayCue` manually.

**Note:** Some of the variables in the parameters structure like `Instigator` might already exist in the `EffectContext`. The `EffectContext` can also contain a `FHitResult` for location of where to spawn the `GameplayCue` in the world. Subclassing `EffectContext` is potentially a good way to pass more data to `GameplayCues`, especially those triggered by a `GameplayEffect`.

See the 3 functions in [`UAbilitySystemGlobals`](#concepts-asg) that populate the `GameplayCueParameters` structure for more information. They are virtual so you can override them to autopopulate more information.

```c++
/** Initialize GameplayCue Parameters */
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectSpecForRPC &Spec);
virtual void InitGameplayCueParameters_GESpec(FGameplayCueParameters& CueParameters, const FGameplayEffectSpec &Spec);
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectContextHandle& EffectContext);
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-manager"></a>
#### 4.8.5 Gameplay Cue Manager
By default, the `GameplayCueManager` will scan the entire game directory for `GameplayCueNotifies` and load them into memory on play. We can change the path where the `GameplayCueManager` scans by setting it in the `DefaultGame.ini`.

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GameplayCueNotifyPaths="/Game/GASDocumentation/Characters"
```

We do want the `GameplayCueManager` to scan and find all of the `GameplayCueNotifies`; however, we don't want it to async load every single one on play. This will put every `GameplayCueNotify` and all of their referenced sounds and particles into memory regardless if they're even used in a level. In a large game like Paragon, this can be hundreds of megabytes of unneeded assets in memory and cause hitching and game freezes on startup.

An alternative to async loading every `GameplayCue` on startup is to only async load `GameplayCues` as they're triggered in-game. This mitigates the unnecessary memory usage and potential game hard freezes while async loading every `GameplayCue` in exchange for potentially delayed effects for the first time that a specific `GameplayCue` is triggered during play. This potential delay is nonexistent for SSDs. I have not tested on a HDD. If using this option in the UE Editor, there may be slight hitches or freezes during the first load of GameplayCues if the Editor needs to compile particle systems. This is not an issue in builds as the particle systems will already be compiled.

First we must subclass `UGameplayCueManager` and tell the `AbilitySystemGlobals` class to use our `UGameplayCueManager` subclass in `DefaultGame.ini`.

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GlobalGameplayCueManagerClass="/Script/ParagonAssets.PBGameplayCueManager"
```

In our `UGameplayCueManager` subclass, override `ShouldAsyncLoadRuntimeObjectLibraries()`.

```c++
virtual bool ShouldAsyncLoadRuntimeObjectLibraries() const override
{
	return false;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-prevention"></a>
#### 4.8.6 Prevent Gameplay Cues from Firing
Sometimes we don't want `GameplayCues` to fire. For example if we block an attack, we may not want to play the hit impact attached to the damage `GameplayEffect` or play a custom one instead. We can do this inside of [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) by calling `OutExecutionOutput.MarkGameplayCuesHandledManually()` and then manually sending our `GameplayCue` event to the `Target` or `Source's` `ASC`.

If you never want any `GameplayCues` to fire on a specific `ASC`, you can set `AbilitySystemComponent->bSuppressGameplayCues = true;`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-batching"></a>
#### 4.8.7 Gameplay Cue Batching
Each `GameplayCue` triggered is an unreliable NetMulticast RPC. In situations where we fire multiple `GCs` at the same time, there are a few optimization methods to condense them down into one RPC or save bandwidth by sending less data.

<a name="concepts-gc-batching-manualrpc"></a>
##### 4.8.7.1 Manual RPC
Say you have a shotgun that shoots eight pellets. That's eight trace and impact `GameplayCues`. [GASShooter](https://github.com/dohi/GASShooter) takes the lazy approach of combining them into one RPC by stashing all of the trace information into the [`EffectContext`](#concepts-ge-ec) as [`TargetData`](#concepts-targeting-data). While this reduces the RPCs from eight to one, it still sends a lot of data over the network in that one RPC (~500 bytes). A more optimized approach is to send an RPC with a custom struct where you efficiently encode the hit locations or maybe you give it a random seed number to recreate/approximate the impact locations on the receiving side. The clients would then unpack this custom struct and turn back into [locally executed `GameplayCues`](#concepts-gc-local).

How this works:
1. Declare a `FScopedGameplayCueSendContext`. This suppresses `UGameplayCueManager::FlushPendingCues()` until it falls out of scope, meaning all `GameplayCues` will be queued up until the `FScopedGameplayCueSendContext` falls out of scope.
1. Override `UGameplayCueManager::FlushPendingCues()` to merge `GameplayCues` that can be batched together based on some custom `GameplayTag` into your custom struct and RPC it to clients.
1. Clients receive the custom struct and unpack it into locally executed `GameplayCues`.

This method can also be used when you need specific parameters for your `GameplayCues` that don't fit with what `GameplayCueParameters` offer and you don't want to add them to the `EffectContext` like damage numbers, crit indicator, broken shield indicator, was fatal hit indicator, etc.

https://forums.unrealengine.com/development-discussion/c-gameplay-programming/1711546-fscopedgameplaycuesendcontext-gameplaycuemanager

<a name="concepts-gc-batching-gcsonge"></a>
##### 4.8.7.2 Multiple GCs on one GE
All of the `GameplayCues` on a `GameplayEffect` are sent in one RPC already. By default, `UGameplayCueManager::InvokeGameplayCueAddedAndWhileActive_FromSpec()` will send the whole `GameplayEffectSpec` (but converted to `FGameplayEffectSpecForRPC`) in the unreliable NetMulticast regardless of the `ASC`'s `Replication Mode`. This could potentially be a lot of bandwidth depending on what is in the `GameplayEffectSpec`. We can potentially optimize this by setting the cvar `AbilitySystem.AlwaysConvertGESpecToGCParams 1`. This will convert `GameplayEffectSpecs` to `FGameplayCueParameter` structures and RPC those instead of the whole `FGameplayEffectSpecForRPC`. This potentially saves bandwidth but also has less information, depending on how the `GESpec` is converted to `GameplayCueParameters` and what your `GCs` need to know.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-events"></a>
#### 4.8.8 Gameplay Cue Events
`GameplayCues` respond to specific `EGameplayCueEvents`:

| `EGameplayCueEvent` | Description                                                                                                                                                                                                                                                                                                                         |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `OnActive`          | Called when a `GameplayCue` is activated (added).                                                                                                                                                                                                                                                                                   |
| `WhileActive`       | Called when `GameplayCue` is active, even if it wasn't actually just applied (Join in progress, etc). This is not `Tick`! It's called once just like `OnActive` when a `GameplayCueNotify_Actor` is added or becomes relevant. If you need `Tick()`, just use the `GameplayCueNotify_Actor`'s `Tick()`. It's an `AActor` after all. |
| `Removed`           | Called when a `GameplayCue` is removed. The Blueprint `GameplayCue` function that responds to this event is `OnRemove`.                                                                                                                                                                                                             |
| `Executed`          | Called when a `GameplayCue` is executed: instant effects or periodic `Tick()`. The Blueprint `GameplayCue` function that responds to this event is `OnExecute`.                                                                                                                                                                     |

Use `OnActive` for anything in your `GameplayCue` that happen at the start of the `GameplayCue` but is okay if late joiners miss. Use `WhileActive` for ongoing effects in the `GameplayCue` that you would want late joiners to see. For example, if you have a `GameplayCue` for a tower structure in a MOBA exploding, you would put the initial explosion particle system and explosion sound in `OnActive` and you would put any residual ongoing fire particles or sounds in the `WhileActive`. In this scenario, it wouldn't make sense for late joiners to replay the initial explosion from `OnActive`, but you would want them to see the persistent, looping fire effects on the ground after the explosion happened from `WhileActive`. `OnRemove` should clean up anything added in `OnActive` and `WhileActive`. `WhileActive` will be called every time an Actor enters the relevancy range of a `GameplayCueNotify_Actor`. `OnRemove` will be called every time an Actor leaves relevancy range of a `GameplayCueNotify_Actor`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-reliability"></a>
#### 4.8.9 Gameplay Cue Reliability

`GameplayCues` in general should be considered unreliable and thus unsuited for anything that directly affects gameplay.

**Executed `GameplayCues`:** These `GameplayCues` are applied via unreliable multicasts and are always unreliable.

**`GameplayCues` applied from `GameplayEffects`:**
* Autonomous proxy reliably receives `OnActive`, `WhileActive`, and `OnRemove`  
`FActiveGameplayEffectsContainer::NetDeltaSerialize()` calls `UAbilitySystemComponent::HandleDeferredGameplayCues()` to call `OnActive` and `WhileActive`. `FActiveGameplayEffectsContainer::RemoveActiveGameplayEffectGrantedTagsAndModifiers()` makes the call to `OnRemoved`.
* Simulated proxies reliably receive `WhileActive` and `OnRemove`  
`UAbilitySystemComponent::MinimalReplicationGameplayCues`'s replication calls `WhileActive` and `OnRemove`. The `OnActive` event is called by an unreliable multicast.

**`GameplayCues` applied without a `GameplayEffect`:**
* Autonomous proxy reliably receives `OnRemove`  
The `OnActive` and `WhileActive` events are called by an unreliable multicast.
* Simulated proxies reliably receive `WhileActive` and `OnRemove`  
`UAbilitySystemComponent::MinimalReplicationGameplayCues`'s replication calls `WhileActive` and `OnRemove`. The `OnActive` event is called by an unreliable multicast.

If you need something in a `GameplayCue` to be 'reliable', then apply it from a `GameplayEffect` and use `WhileActive` to add the FX and `OnRemove` to remove the FX.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-asg"></a>
### 4.9 Ability System Globals
The [`AbilitySystemGlobals`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemGlobals/index.html) class holds global information about GAS. Most of the variables can be set from the `DefaultGame.ini`. Generally you won't have to interact with this class, but you should be aware of its existence. If you need to subclass things like the [`GameplayCueManager`](#concepts-gc-manager) or the [`GameplayEffectContext`](#concepts-ge-context), you have to do that through the `AbilitySystemGlobals`.

To subclass `AbilitySystemGlobals`, set the class name in the `DefaultGame.ini`:
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
AbilitySystemGlobalsClassName="/Script/ParagonAssets.PAAbilitySystemGlobals"
```

<a name="concepts-asg-initglobaldata"></a>
#### 4.9.1 InitGlobalData()
Starting in UE 4.24, it is now necessary to call `UAbilitySystemGlobals::InitGlobalData()` to use [`TargetData`](#concepts-targeting-data), otherwise you will get errors related to `ScriptStructCache` and clients will be disconnected from the server. This function only needs to be called once in a project. Fortnite calls it from the AssetManager class's start initial loading function and Paragon called it from `UEngine::Init()`. I find that putting it in `UEngineSubsystem::Initialize()` is a good place as shown in the Sample Project. I would consider this boilerplate code that you should copy into your project to avoid issues with `TargetData`.

If you run into a crash while using the `AbilitySystemGlobals` `GlobalAttributeSetDefaultsTableNames`, you may need to call `UAbilitySystemGlobals::InitGlobalData()` later like Fortnite in the `AssetManager` or in the `GameInstance` instead of in `UEngineSubsystem::Initialize()`. This crash is likely due to the order in which the `Subsystems` are created and the `GlobalAttributeDefaultsTables` requires the `EditorSubsystem` to be loaded to bind a delegate in `UAbilitySystemGlobals::InitGlobalData()`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p"></a>
### 4.10 Prediction
GAS comes out of the box with support for client-side prediction; however, it does not predict everything. Client-side prediction in GAS means that the client does not have to wait for the server's permission to activate a `GameplayAbility` and apply `GameplayEffects`. It can "predict" the server giving it permission to do this and predict the targets that it would apply `GameplayEffects` to. The server then runs the `GameplayAbility` network latency-time after the client activates and tells the client if he was correct or not in his predictions. If the client was wrong in any of his predictions, he will "roll back" his changes from his "mispredictions" to match the server.

The definitive source for GAS-related prediction is `GameplayPrediction.h` in the plugin source code.

Epic's mindset is to only predict what you "can get away with". For example, Paragon and Fortnite do not predict damage. Most likely they use [`ExecutionCalculations`](#concepts-ge-ec) for their damage which cannot be predicted anyway. This is not to say that you can't try to predict certain things like damage. By all means if you do it and it works well for you then that's great.

> ... we are also not all in on a "predict everything: seamlessly and automatically" solution. We still feel player prediction is best kept to a minimum (meaning: predict the minimum amount of stuff you can get away with).

*Dave Ratti from Epic's comment from the new [Network Prediction Plugin](#concepts-p-npp)*

**What is predicted:**
> * Ability activation
> *	Triggered Events
> *	GameplayEffect application:
>    * Attribute modification (EXCEPTIONS: Executions do not currently predict, only attribute modifiers)
>    * GameplayTag modification
> * Gameplay Cue events (both from within predictive gameplay effect and on their own)
> * Montages
> * Movement (built into UE4 UCharacterMovement)

**What is not predicted:**
> * GameplayEffect removal
> * GameplayEffect periodic effects (dots ticking)

*From `GameplayPrediction.h`*

While we can predict `GameplayEffect` application, we cannot predict `GameplayEffect` removal. One way that we can work around this limitation is to predict the inverse effect when we want to remove a `GameplayEffect`. Say we predict a movement speed slow of 40%. We can predictively remove it by applying a movement speed buff of 40%. Then remove both `GameplayEffects` at the same time. This is not appropriate for every scenario and support for predicting `GameplayEffect` removal is still needed. Dave Ratti from Epic has expressed desire to add it to a [future iteration of GAS](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89).

Because we cannot predict the removal of `GameplayEffects`, we cannot fully predict `GameplayAbility` cooldowns and there is no inverse `GameplayEffect` workaround for them. The server's replicated `Cooldown GE` will exist on the client and any attempts to bypass this (with `Minimal` replication mode for example) will be rejected by the server. This means clients with higher latencies take longer to tell the server to go on cooldown and to receive the removal of the server's `Cooldown GE`. This means players with higher latencies will have a lower rate of fire than players with lower latencies, giving them a disadvantage against lower latency players. Fortnite avoids this issue by using custom bookkeeping instead of `Cooldown GEs`.

Regarding predicting damage, I personally do not recommend it despite it being one of the first things that most people try when starting with GAS. I especially do not recommend trying to predict death. While you can predict damage, doing so is tricky. If you mispredict applying damage, the player will see the enemy's health jump back up. This can be especially awkward and frustrating if you try to predict death. Say you mispredict a `Character's` death and it starts ragdolling only to stop ragdolling and continue shooting at you when the server corrects it.

**Note:** `Instant`	`GameplayEffects` (like `Cost GEs`) that change `Attributes` can be predicted on yourself seamlessly, predicting `Instant` `Attribute` changes to other characters will show a brief anomaly or "blip" in their `Attributes`. Predicted `Instant` `GameplayEffects` are actually treated like `Infinite` `GameplayEffects` so that they can be rolled back if mispredicted. When the server's `GameplayEffect` is applied, there potentially exists two of the same `GameplayEffect's` causing the `Modifier` to be applied twice or not at all for a brief moment. It will eventually correct itself but sometimes the blip is noticeable to players.

Problems that GAS's prediction implementation is trying to solve:
> 1. "Can I do this?" Basic protocol for prediction.
> 2. "Undo" How to undo side effects when a prediction fails.
> 3. "Redo" How to avoid replaying side effects that we predicted locally but that also get replicated from the server.
> 4. "Completeness" How to be sure we /really/ predicted all side effects.
> 5. "Dependencies" How to manage dependent prediction and chains of predicted events.
> 6. "Override" How to override state predictively that is otherwise replicated/owned by the server.

*From `GameplayPrediction.h`*

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-key"></a>
#### 4.10.1 Prediction Key
GAS's prediction works on the concept of a `Prediction Key` which is an integer identifier that the client generates when he activates a `GameplayAbility`.

* Client generates a prediction key when it activates a `GameplayAbility. This is the `Activation Prediction Key`.
* Client sends this prediction key to the server with `CallServerTryActivateAbility()`.
* Client adds this prediction key to all `GameplayEffects` that it applies while the prediction key is valid.
* Client's prediction key falls out of scope. Further predicted effects in the same `GameplayAbility` need a new [Scoped Prediction Window](#concepts-p-windows).


* Server receives the prediction key from the client.
* Server adds this prediction key to all `GameplayEffects` that it applies.
* Server replicates the prediction key back to the client.


* Client receives replicated `GameplayEffects` from the server with the prediction key used to apply them. If any of the replicated `GameplayEffects` match the `GameplayEffects` that the client applied with the same prediction key, they were predicted correctly. There will temporarily be two copies of the `GameplayEffect` on the target until the client removes its predicted one.
* Client receives the prediction key back from the server. This is the `Replicated Prediction Key`. This prediction key is now marked stale.
* Client removes **all** `GameplayEffects` that it created with the now stale replicated prediction key. `GameplayEffects` replicated by the server will persist. Any `GameplayEffects` that the client added and didn't receive a matching replicated version from the server were mispredicted.

Prediction keys are guaranteed to be valid during an atomic grouping of instructions "window" in `GameplayAbilities` starting with `Activation` from the activation prediction key. You can think of this as being only valid during one frame. Any callbacks from latent action `AbilityTasks` will no longer have a valid prediction key unless the `AbilityTask` has a built-in Synch Point which generates a new [Scoped Prediction Window](#concepts-p-windows).

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-windows"></a>
#### 4.10.2 Creating New Prediction Windows in Abilities
To predict more actions in callbacks from `AbilityTasks`, we need to create a new Scoped Prediction Window with a new Scoped Prediction Key. This is sometimes referred to as a Synch Point between the client and server. Some `AbilityTasks` like all of the input related ones come with built-in functionality to create a new scoped prediction window, meaning atomic code in the `AbilityTasks'` callbacks have a valid scoped prediction key to use. Other tasks like the `WaitDelay` task do not have built-in code to create a new scoped prediction window for its callback. If you need to predict actions after an `AbilityTask` that does not have built-in code to create a scoped prediction window like `WaitDelay`, we must manually do that using the `WaitNetSync` `AbilityTask` with the option `OnlyServerWait`. When the client hits a `WaitNetSync` with `OnlyServerWait`, it generates a new scoped prediction key based on the `GameplayAbility's` activation prediction key, RPCs it to the server, and adds it to any new `GameplayEffects` that it applies. When the server hits a `WaitNetSync` with `OnlyServerWait`, it waits until it receives the new scoped prediction key from the client before continuing. This scoped prediction key does the same dance as activation prediction keys - applied to `GameplayEffects` and replicated back to clients to be marked stale. The scoped prediction key is valid until it falls out of scope, meaning the scoped prediction window has closed. So again, only atomic operations, nothing latent, can use the new scoped prediction key.

You can create as many scoped prediction windows as you need.

If you would like to add the synch point functionality to your own custom `AbilityTasks`, look at how the input ones essentially inject the `WaitNetSync` `AbilityTask` code into them.

**Note:** When using `WaitNetSync`, this does block the server's `GameplayAbility` from continuing execution until it hears from the client. This could potentially be abused by malicious users who hack the game and intentionally delay sending their new scoped prediction key. While Epic uses the `WaitNetSync` sparingly, it recommends potentially building a new version of the `AbilityTask` with a delay that automatically continues without the client if this is a concern for you.

The Sample Project uses `WaitNetSync` in the Sprint `GameplayAbility` to create a new scoped prediction window every time we apply the stamina cost so that we can predict it. Ideally we want a valid prediction key when applying costs and cooldowns.

If you have a predicted `GameplayEffect` that is playing twice on the owning client, your prediction key is stale and you're experiencing the "redo" problem. You can usually solve this by putting a `WaitNetSync` `AbilityTask` with `OnlyServerWait` right before you apply the `GameplayEffect` to create a new scoped prediction key.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-spawn"></a>
#### 4.10.3 Predictively Spawning Actors
Spawning `Actors` predictively on clients is an advanced topic. GAS does not provide functionality to handle this out of the box (the `SpawnActor` `AbilityTask` only spawns the `Actor` on the server). The key concept is to spawn a replicated `Actor` on both the client and the server.

If the `Actor` is just cosmetic or doesn't serve any gameplay purpose, the simple solution is to override the `Actor's` `IsNetRelevantFor()` function to restrict the server from replicating to the owning client. The owning client would have his locally spawned version and the server and other clients would have the server's replicated version.
```c++
bool APAReplicatedActorExceptOwner::IsNetRelevantFor(const AActor * RealViewer, const AActor * ViewTarget, const FVector & SrcLocation) const
{
	return !IsOwnedBy(ViewTarget);
}
```

If the spawned `Actor` affects gameplay like a projectile that needs to predict damage, then you need advanced logic that is outside of the scope of this documentation. Look at how UnrealTournament predictively spawns projectiles on Epic Games' GitHub. They have a dummy projectile spawned only on the owning client that synchs up with the server's replicated projectile.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-future"></a>
#### 4.10.4 Future of Prediction in GAS
`GameplayPrediction.h` states in the future they could potentially add functionality for predicting `GameplayEffect` removal and periodic `GameplayEffects`.

Dave Ratti from Epic has [expressed interest](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89) in fixing the `latency reconciliation` problem for predicting cooldowns, disadvantaging players with higher latencies versus players with lower latencies.

The new [`Network Prediction` plugin](#concepts-p-npp) by Epic is expected to be fully interoperable with the GAS like the `CharacterMovementComponent` *was* before it.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-npp"></a>
#### 4.10.5 Network Prediction Plugin
Epic recently started an initiative to replace the `CharacterMovementComponent` with a new `Network Prediction` plugin. This plugin is still in its very early stages but is available to very early access on the Unreal Engine GitHub. It's too soon to tell which future version of the Engine that it will make its experimental beta debut in.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting"></a>
### 4.11 Targeting

<a name="concepts-targeting-data"></a>
#### 4.11.1 Target Data
[`FGameplayAbilityTargetData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetData/index.html) is a generic structure for targeting data meant to be passed across the network. `TargetData` will typically hold `AActor`/`UObject` references, `FHitResults`, and other generic location/direction/origin information. However, you can subclass it to put essentially anything that you want inside of them as a simple means to [pass data between the client and server in `GameplayAbilities`](#concepts-ga-data). The base struct `FGameplayAbilityTargetData` is not meant to be used directly but instead subclassed. `GAS` comes with a few subclassed `FGameplayAbilityTargetData` structs out of the box located in `GameplayAbilityTargetTypes.h`.

`TargetData` is typically produced by [`Target Actors`](#concepts-targeting-actors) or **created manually** and consumed by [`AbilityTasks`](##concepts-at) and [`GameplayEffects`](#concepts-ge) via the [`EffectContext`](#concepts-ge-context). As a result of being in the `EffectContext`, [`Executions`](#concepts-ge-ec), [`MMCs`](#concepts-ge-mmc), [`GameplayCues`](#concepts-gc), and the functions on the backend of the [`AttributeSet`](#concepts-as) can access the `TargetData`.

We don't typically pass around the `FGameplayAbilityTargetData` directly, instead we use a [`FGameplayAbilityTargetDataHandle`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetDataHandle/index.html) which has an internal TArray of pointers to `FGameplayAbilityTargetData`. This intermediate struct provides support for polymorphism of the `TargetData`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-actors"></a>
#### 4.11.2 Target Actors
`GameplayAbilities` spawn [`TargetActors`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor/index.html) with the `WaitTargetData` `AbilityTask` to visualize and capture targeting information from the world. `TargetActors` may optionally use [`GameplayAbilityWorldReticles`](#concepts-targeting-reticles) to display current targets. Upon confirmation, the targeting information is returned as [`TargetData`](#concepts-targeting-data) which can then be passed into `GameplayEffects`.
 
`TargetActors` are based on `AActor` so they can have any kind of visible component to represent **where** and **how** they are targeting such as static meshes or decals. Static meshes may be used to visualize placement of an object that your character will build. Decals may be used to show an area of effect on the ground. The Sample Project uses [`AGameplayAbilityTargetActor_GroundTrace`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor_Grou-/index.html) with a decal on the ground to represent the damage area of effect for the Meteor ability. They also don't need to display anything either. For example it wouldn't make sense to display anything for a hitscan gun that instantly traces a line to its target as used in [GASShooter](https://github.com/dohi/GASShooter).

They capture targeting information using basic traces or collision overlaps and convert the results as `FHitResults` or `AActor` arrays to `TargetData` depending on the `TargetActor` implementation. The `WaitTargetData` `AbilityTask` determines when the targets are confirmed through its `TEnumAsByte<EGameplayTargetingConfirmation::Type> ConfirmationType` parameter. When **not** using `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant`, the `TargetActor` typically performs the trace/overlap on `Tick()` and updates its location to the `FHitResult` depending on its implementation. While this performs a trace/overlap on `Tick()`, it's generally not terrible since it's not replicated and you typically don't have more than one (although you could have more) `TargetActor` running at a time. Just be aware that it uses `Tick()` and some complex `TargetActors` might do a lot on it like the rocket launcher's secondary ability in GASShooter. While tracing on `Tick()` is very responsive to the client, you may consider lowering the tick rate on the `TargetActor` if the performance hit is too much. In the case of `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant`, the `TargetActor` immediately spawns, produces `TargetData`, and destroys. `Tick()` is never called. 

| `EGameplayTargetingConfirmation::Type` | When targets are confirmed                                                                                                                                                                                                                                                                                                                                     |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Instant`                              | The targeting happens instantly without special logic or user input deciding when to 'fire'.                                                                                                                                                                                                                                                                   |
| `UserConfirmed`                        | The targeting happens when the user confirms the targeting when the [ability is bound to a `Confirm` input](#concepts-ga-input) or by calling `UAbilitySystemComponent::TargetConfirm()`. The `TargetActor` will also respond to a bound `Cancel` input or call to `UAbilitySystemComponent::TargetCancel()` to cancel targeting.                              |
| `Custom`                               | The GameplayTargeting Ability is responsible for deciding when the targeting data is ready by calling `UGameplayAbility::ConfirmTaskByInstanceName()`. The `TargetActor` will also respond to `UGameplayAbility::CancelTaskByInstanceName()` to cancel targeting.                                                                                              |
| `CustomMulti`                          | The GameplayTargeting Ability is responsible for deciding when the targeting data is ready by calling `UGameplayAbility::ConfirmTaskByInstanceName()`. The `TargetActor` will also respond to `UGameplayAbility::CancelTaskByInstanceName()` to cancel targeting. Should not end the `AbilityTask` upon data production.                                       |

Not every EGameplayTargetingConfirmation::Type is supported by every `TargetActor`. For example, `AGameplayAbilityTargetActor_GroundTrace` does not support `Instant` confirmation.

The `WaitTargetData` `AbilityTask` takes in a `AGameplayAbilityTargetActor` class as a parameter and will spawn an instance on each activation of the `AbilityTask` and will destroy the `TargetActor` when the `AbilityTask` ends. The `WaitTargetDataUsingActor` `AbilityTask` takes in an already spawned `TargetActor`, but still destroys it when the `AbilityTask` ends. Both of these `AbilityTasks` are inefficient in that they either spawn or require a newly spawned `TargetActor` for each use. They're great for prototyping, but in production you might explore optimizing it if you have cases where you are constantly producing `TargetData` like in the case of an automatic rifle. GASShooter has a custom subclass of [`AGameplayAbilityTargetActor`](https://github.com/dohi/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/GSGATA_Trace.h) and a new [`WaitTargetDataWithReusableActor`](https://github.com/dohi/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/AbilityTasks/GSAT_WaitTargetDataUsingActor.h) `AbilityTask` written from scratch that allows you to reuse a `TargetActor` without destroying it.

`TargetActors` are not replicated by default; however, they can be made to replicate if that makes sense in your game to show other players where the local player is targeting. They do include default functionality to communicate with the server via RPCs on the `WaitTargetData` `AbilityTask`. If the `TargetActor`'s `ShouldProduceTargetDataOnServer` property is set to `false`, then the client will RPC its `TargetData` to the server on confirmation via `CallServerSetReplicatedTargetData()` in `UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()`. If `ShouldProduceTargetDataOnServer` is `true`, the client will send a generic confirm event, `EAbilityGenericReplicatedEvent::GenericConfirm`, RPC to the server in `UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()` and the server will do the trace or overlap check upon receiving the RPC to produce data on the server. If the client cancels the targeting, it will send a generic cancel event, `EAbilityGenericReplicatedEvent::GenericCancel`, RPC to the server in `UAbilityTask_WaitTargetData::OnTargetDataCancelledCallback`. As you can see, there are a lot of delegates on both the `TargetActor` and the `WaitTargetData` `AbilityTask`. The `TargetActor` responds to inputs to produce and broadcast `TargetData` ready, confirm, or cancel delegates. `WaitTargetData` listens to the `TargetActor`'s `TargetData` ready, confirm, and cancel delegates and relays that information back to the `GameplayAbility` and to the server. If you send `TargetData` to the server, you may want to do validation on the server to make sure the `TargetData` looks reasonable to prevent cheating. Producing the `TargetData` directly on the server avoids this issue entirely, but will potentially lead to mispredictions for the owning client.

Depending on the particular subclass of `AGameplayAbilityTargetActor` that you use, different `ExposeOnSpawn` parameters will be exposed on the `WaitTargetData` `AbilityTask` node. Some common parameters include:

| Common `TargetActor` Parameters | Definition                                                                                                                                                                                                                                                                                                               |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Debug                           | If `true`, it will draw debug tracing/overlapping information whenever the `TargetActor` performs a trace in non-shipping builds. Remember, non-`Instant` `TargetActors` will perform a trace on `Tick()` so these debug draw calls will also happen on `Tick()`.                                                        |
| Filter                          | [Optional] A special struct for filtering out (removing) `Actors` from the targets when the trace/overlap happens. Typical use cases are to filter out the player's `Pawn`, require targets be of a specific class. See [Target Data Filters](#concepts-target-data-filters) for more advanced use cases. |
| Reticle Class                   | [Optional] Subclass of `AGameplayAbilityWorldReticle` that the `TargetActor` will spawn.                                                                                                                                                                                                                                 |
| Reticle Parameters              | [Optional] Configure your Reticles. See [Reticles](#concepts-targeting-reticles).                                                                                                                                                                                                                                        |
| Start Location                  | A special struct for where tracing should start from. Typically this will be the player's viewpoint, a weapon muzzle, or the `Pawn`'s location.                                                                                                                                                                          |

With the default `TargetActor` classes, `Actors` are only valid targets when they are directly in the trace/overlap. If they leave the trace/overlap (they move or you look away), they are no longer valid. If you want the `TargetActor` to remember the last valid target(s), you will need to add this functionality to a custom `TargetActor` class. I refer to these as persistent targets as they will persist until the `TargetActor` receives confirmation or cancellation, the `TargetActor` finds a new valid target in its trace/overlap, or the target is no longer valid (destroyed). GASShooter uses persistent targets for its rocket launcher's secondary ability's homing rockets targeting.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-target-data-filters"></a>
#### 4.11.3 Target Data Filters
Using both the `Make GameplayTargetDataFilter` and `Make Filter Handle` nodes, you can filter out the player's `Pawn` or select only a specific class. If you need more advanced filtering, you can subclass `FGameplayTargetDataFilter` and override the `FilterPassesForActor` function. 
```c++
USTRUCT(BlueprintType)
struct GASDOCUMENTATION_API FGDNameTargetDataFilter : public FGameplayTargetDataFilter
{
	GENERATED_BODY()

	/** Returns true if the actor passes the filter and will be targeted */
	virtual bool FilterPassesForActor(const AActor* ActorToBeFiltered) const override;
};
```

However, this will not work directly into the `Wait Target Data` node as it requires a `FGameplayTargetDataFilterHandle`. A new custom `Make Filter Handle` must be made to accept the subclass:
```c++
FGameplayTargetDataFilterHandle UGDTargetDataFilterBlueprintLibrary::MakeGDNameFilterHandle(FGDNameTargetDataFilter Filter, AActor* FilterActor)
{
	FGameplayTargetDataFilter* NewFilter = new FGDNameTargetDataFilter(Filter);
	NewFilter->InitializeFilterContext(FilterActor);

	FGameplayTargetDataFilterHandle FilterHandle;
	FilterHandle.Filter = TSharedPtr<FGameplayTargetDataFilter>(NewFilter);
	return FilterHandle;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-reticles"></a>
#### 4.11.4 Gameplay Ability World Reticles
[`AGameplayAbilityWorldReticles`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityWorldReticle/index.html) (`Reticles`) visualize **who** you are targeting when targeting with non-`Instant` confirmed [`TargetActors`](#concepts-targeting-actors). `TargetActors` are responsible for the spawn and destroy lifetimes for all `Reticles`. `Reticles` are `AActors` so they can use any kind of visual component for representation. A common implementation as seen in [GASShooter](https://github.com/dohi/GASShooter) is to use a `WidgetComponent` to display a UMG Widget in screen space (always facing the player's camera). `Reticles` do not know which `AActor` that they're on, but you could subclass in that functionality on a custom `TargetActor`. `TargetActors` will typically update the `Reticle`'s location to the target's location on every `Tick()`.

GASShooter uses `Reticles` to show locked-on targets for the rocket launcher's secondary ability's homing rockets. The red indicator on the enemy is the `Reticle`. The similar white image is the rocket launcher's crosshair.
![Reticles in GASShooter](https://github.com/dohi/GASDocumentation/raw/master/Images/gameplayabilityworldreticle.png)

`Reticles` come with a handful of `BlueprintImplementableEvents` for designers (they're intended to be developed in Blueprints):

```c++
/** Called whenever bIsTargetValid changes value. */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnValidTargetChanged(bool bNewValue);

/** Called whenever bIsTargetAnActor changes value. */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnTargetingAnActor(bool bNewValue);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnParametersInitialized();

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamFloat(FName ParamName, float value);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamVector(FName ParamName, FVector value);
```

`Reticles` can optionally use [`FWorldReticleParameters`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FWorldReticleParameters/index.html) provided by the `TargetActor` for configuration. The default struct only provides one variable `FVector AOEScale`. While you can technically subclass this struct, the `TargetActor` will only accept the base struct. It seems a little short-sighted to not allow this to be subclassed with default `TargetActors`. However, if you make your own custom `TargetActor`, you can provide your own custom reticle parameters struct and manually pass it to your subclass of `AGameplayAbilityWorldReticles` when you spawn them.

`Reticles` are not replicated by default, but can be made replicated if it makes sense for your game to show other players who the local player is targeting.

`Reticles` will only display on the current valid target with the default `TargetActors`. For example, if you're using a `AGameplayAbilityTargetActor_SingleLineTrace` to trace for a target, the `Reticle` will only appear when the enemy is directly in the trace path. If you look away, the enemy is no longer a valid target and the `Reticle` will disappear. If you want the `Reticle` to stay on the last valid target, you will want to customize your `TargetActor` to remember the last valid target and keep the `Reticle` on them. I refer to these as persistent targets as they will persist until the `TargetActor` receives confirmation or cancellation, the `TargetActor` finds a new valid target in its trace/overlap, or the target is no longer valid (destroyed).  GASShooter uses persistent targets for its rocket launcher's secondary ability's homing rockets targeting.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-containers"></a>
#### 4.11.5 Gameplay Effect Containers Targeting
[`GameplayEffectContainers`](#concepts-ge-containers) come with an optional, efficient means of producing [`TargetData`](#concepts-targeting-data). This targeting takes places instantly when the `EffectContainer` is applied on the client and the server. It's more efficient than [`TargetActors`](#concepts-targeting-actors) because it runs on the CDO of the targeting object (no spawning and destroying of `Actors`), but it lacks player input, happens instantly without needing confirmation, cannot be canceled, and cannot send data from the client to the server (produces data on both). It works well for instant traces and collision overlaps. Epic's [Action RPG Sample Project](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) includes two example types of targeting with its containers - target the ability owner and pull `TargetData` from an event. It also implements one in Blueprint to do instant sphere traces at some offset (set by child Blueprint classes) from the player. You can subclass `URPGTargetType` in C++ or Blueprint to make your own targeting types.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae"></a>
## 5. Commonly Implemented Abilities and Effects

<a name="cae-stun"></a>
### 5.1 Stun
Typically with stuns, we want to cancel all of a `Character's` active `GameplayAbilities`, prevent new `GameplayAbility` activations, and prevent movement throughout the duration of the stun. The Sample Project's Meteor `GameplayAbility` applies a stun on hit targets.

To cancel the target's active `GameplayAbilities`, we call `AbilitySystemComponent->CancelAbilities()` when the stun [`GameplayTag` is added](#concepts-gt-change).

To prevent new `GameplayAbilities` from activating while stunned, the `GameplayAbilities` are given the stun `GameplayTag` in their [`Activation Blocked Tags` `GameplayTagContainer`](#concepts-ga-tags).

To prevent movement while stunned, we override the `CharacterMovementComponent's` `GetMaxSpeed()` function to return 0 when the owner has the stun `GameplayTag`.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-sprint"></a>
### 5.2 Sprint
The Sample Project provides an example of how to sprint - run faster while `Left Shift` is held down.

The faster movement is handled predictively by the `CharacterMovementComponent` by sending a flag over the network to the server. See `GDCharacterMovementComponent.h/cpp` for details.

The `GA` handles responding to the `Left Shift` input, tells the `CMC` to begin and stop sprinting, and to predictively charge stamina while `Left Shift` is pressed. See `GA_Sprint_BP` for details.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-ads"></a>
### 5.3 Aim Down Sights
The Sample Project handles this the exact same way as sprinting but decreasing the movement speed instead of increasing it.

See `GDCharacterMovementComponent.h/cpp` for details on predictively decreasing the movement speed.

See `GA_AimDownSight_BP` for details on handling the input. There is no stamina cost for aiming down sights.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-ls"></a>
### 5.4 Lifesteal
I handle lifesteal inside of the damage [`ExecutionCalculation`](#concepts-ge-ec). The `GameplayEffect` will have a `GameplayTag` on it like `Effect.CanLifesteal`. The `ExecutionCalculation` checks if the `GameplayEffectSpec` has that `Effect.CanLifesteal` `GameplayTag`. If the `GameplayTag` exists, the `ExecutionCalculation` [creates a dynamic `Instant` `GameplayEffect`](#concepts-ge-dynamic) with the amount of health to give as the modifier and applies it back to the `Source's` `ASC`.

```c++
if (SpecAssetTags.HasTag(FGameplayTag::RequestGameplayTag(FName("Effect.Damage.CanLifesteal"))))
{
	float Lifesteal = Damage * LifestealPercent;

	UGameplayEffect* GELifesteal = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Lifesteal")));
	GELifesteal->DurationPolicy = EGameplayEffectDurationType::Instant;

	int32 Idx = GELifesteal->Modifiers.Num();
	GELifesteal->Modifiers.SetNum(Idx + 1);
	FGameplayModifierInfo& Info = GELifesteal->Modifiers[Idx];
	Info.ModifierMagnitude = FScalableFloat(Lifesteal);
	Info.ModifierOp = EGameplayModOp::Additive;
	Info.Attribute = UPAAttributeSetBase::GetHealthAttribute();

	SourceAbilitySystemComponent->ApplyGameplayEffectToSelf(GELifesteal, 1.0f, SourceAbilitySystemComponent->MakeEffectContext());
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-random"></a>
### 5.5 Generating a Random Number on Client and Server
Sometimes you need to generate a "random" number inside of a `GameplayAbility` for things like bullet recoil or spread. The client and the server will both want to generate the same random numbers. To do this, we must set the `random seed` to be the same at the time of `GameplayAbility` activation. You will want to set the `random seed` each time you activate the `GameplayAbility` in case the client mispredicts activation and its random number sequence becomes out of synch with the server's.

| Seed Setting Method                                                          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Use the activation prediction key                                            | The `GameplayAbility` activation prediction key is an int16 guaranteed to be synchronized and available in both the client and server in the `Activation()`. You can set this as the `random seed` on both the client and the server. The downside to this method is that the prediction key always starts at zero each time the game starts and consistently increments the value to use between generating keys. This means each match will have the exact same random number sequence. This may or may not be random enough for your needs. |
| Send a seed through an event payload when you activate the `GameplayAbility` | Activate your `GameplayAbility` by event and send the randomly generated seed from the client to the server via the replicated event payload. This allows for more randomness but the client could easily hack their game to only send the same seed value every time. Also activating `GameplayAbilities` by event will prevent them from activating from the input bind.                                                                                                                                                                     |

If your random deviation is small, most players won't notice that the sequence is the same every game and using the activation prediction key as the `random seed` should work for you. If you're doing something more complex that needs to be hacker proof, perhaps using a `Server Initiated` `GameplayAbility` would work better where the server can create the prediction key or generate the `random seed` to send via an event payload.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-crit"></a>
### 5.6 Critical Hits
I handle critical hits inside of the damage [`ExecutionCalculation`](#concepts-ge-ec). The `GameplayEffect` will have a `GameplayTag` on it like `Effect.CanCrit`. The `ExecutionCalculation` checks if the `GameplayEffectSpec` has that `Effect.CanCrit` `GameplayTag`. If the `GameplayTag` exists, the `ExecutionCalculation` generates a random number corresponding to the critical hit chance (`Attribute` captured from the `Source`) and adds the critical hit damage (also an `Attribute` captured from the `Source`) if it succeeded. Since I don't predict damage, I don't have to worry about synchronizing the random number generators on the client and server since the `ExecutionCalculation` will only run on the server. If you tried to do this predictively using an `MMC` to do your damage calculation, you would have to get a reference to the `random seed` from the `GameplayEffectSpec->GameplayEffectContext->GameplayAbilityInstance`.

See how [GASShooter](https://github.com/dohi/GASShooter) does headshots. It's the same concept except that it does not rely on a random number for chance and instead checks the `FHitResult` bone name.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-nonstackingge"></a>
### 5.7 Non-Stacking Gameplay Effects but Only the Greatest Magnitude Actually Affects the Target
Slow effects in Paragon did not stack. Each slow instance applied and kept track of their lifetimes as normal, but only the greatest magnitude slow effect actually affected the `Character`. GAS provides for this scenario out of the box with `AggregatorEvaluateMetaData`. See [`AggregatorEvaluateMetaData()`](#concepts-as-onattributeaggregatorcreated) for details and implementation.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-paused"></a>
### 5.8 Generate Target Data While Game is Paused
If you need to pause the game while waiting to generate [`TargetData`](#concepts-targeting-data) from a `WaitTargetData` `AbilityTask` from your player, I suggest instead of pausing to use `slomo 0`.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-onebuttoninteractionsystem"></a>
### 5.9 One Button Interaction System
[GASShooter](https://github.com/dohi/GASShooter) implements a one button interaction system where the player can press or hold 'E' to interact with interactable objects like reviving a player, opening a weapon chest, and opening or closing a sliding door.

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging"></a>
## 6. Debugging GAS
Often when debugging GAS related issues, you want to know things like:
> * "What are the values of my attributes?"
> * "What gameplay tags do I have?"
> * "What gameplay effects do I currently have?"
> * "What abilities do I have granted, which ones are running, and which ones are blocked from activating?".

GAS comes with two techniques for answering these questions at runtime - [`showdebug abilitysystem`](#debugging-sd) and hooks in the [`GameplayDebugger`](#debugging-gd).

**Tip:** UE4 likes to optimize C++ code which makes it hard to debug some functions. You will encounter this rarely when tracing deep into your code. If setting your Visual Studio solution configuration to `DebugGame Editor` still prevents tracing code or inspecting variables, you can disable all optimizations by wrapping the optimized function with the `PRAGMA_DISABLE_OPTIMIZATION_ACTUAL` and `PRAGMA_ENABLE_OPTIMIZATION_ACTUAL` macros. This cannot be used on the plugin code unless you rebuild the plugin from source. This may or may not work on inline functions depending on what they do and where they are. Be sure to remove the macros when you're done debugging!

```c++
PRAGMA_DISABLE_OPTIMIZATION_ACTUAL
void MyClass::MyFunction(int32 MyIntParameter)
{
	// My code
}
PRAGMA_ENABLE_OPTIMIZATION_ACTUAL
```

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-sd"></a>
### 6.1 showdebug abilitysystem
Type `showdebug abilitysystem` in the in-game console. This feature is split into three "pages". All three pages will show the `GameplayTags` that you currently have. Type `AbilitySystem.Debug.NextCategory` into the console to cycle between the pages.

The first page shows the `CurrentValue` of all of your `Attributes`:
![First Page of showdebug abilitysystem](https://github.com/dohi/GASDocumentation/raw/master/Images/showdebugpage1.png)

The second page shows all of the `Duration` and `Infinite` `GameplayEffects` on you, their number of stacks, what `GameplayTags` they give, and what `Modifiers` they give.
![Second Page of showdebug abilitysystem](https://github.com/dohi/GASDocumentation/raw/master/Images/showdebugpage2.png)

The third page shows all of the `GameplayAbilities` that have been granted to you, whether they are currently running, whether they are blocked from activating, and the status of currently running `AbilityTasks`.
![Third Page of showdebug abilitysystem](https://github.com/dohi/GASDocumentation/raw/master/Images/showdebugpage3.png)

While you can cycle between targets with `PageUp` and `PageDown`, the pages will only show data for the `ASC` on your locally controlled `Character`. However, using `AbilitySystem.Debug.NextTarget` and `AbilitySystem.Debug.PrevTarget` will show data for other `ASCs`, but it will not update the top half of the debug information nor will it update the green targeting rectangular prism so there is no way to know which `ASC` is currently being targeted. This bug has been reported https://issues.unrealengine.com/issue/UE-90437.

**Note:** For `showdebug abilitysystem` to work an actual HUD class must be selected in the GameMode. Otherwise the command is not found and "Unknown Command" is returned.

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-gd"></a>
### 6.2 Gameplay Debugger
GAS adds functionality to the Gameplay Debugger. Access the Gameplay Debugger with the Apostrophe (') key. Enable the Abilities category by pressing 3 on your numpad. The category may be different depending on what plugins you have. If your keyboard doesn't have a numpad like a laptop, then you can change the keybindings in the project settings.

Use the Gameplay Debugger when you want to see the `GameplayTags`, `GameplayEffects`, and `GameplayAbilities` on **other** `Characters`. Unfortunately it does not show the `CurrentValue` of the target's `Attributes`. It will target whatever `Character` is in the center of your screen. You can change targets by selecting them in the World Outliner in the Editor or by looking at a different `Character` and press Apostrophe (') again. The currently inspected `Character` has the largest red circle above it.

![Gameplay Debugger](https://github.com/dohi/GASDocumentation/raw/master/Images/gameplaydebugger.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-log"></a>
### 6.3 GAS Logging
The GAS source code contains a lot of logging statements produced at varying verbosity levels. You will most likely see these as `ABILITY_LOG()` statements. The default verbosity level is `Display`. Anything higher will not be displayed in the console by default.

To change the verbosity level of a log category, type into your console:

```
log [category] [verbosity]
```

For example, to turn on `ABILITY_LOG()` statements, you would type into your console:
```
log LogAbilitySystem VeryVerbose
```

To reset it back to default, type:
```
log LogAbilitySystem Display
```

To display all log categories, type:
```
log list
```

Notable GAS related logging categories:

| Logging Category          | Default Verbosity Level |
| ------------------------- | ----------------------- |
| LogAbilitySystem          | Display                 |
| LogAbilitySystemComponent | Log                     |
| LogGameplayCueDetails     | Log                     |
| LogGameplayCueTranslator  | Display                 |
| LogGameplayEffectDetails  | Log                     |
| LogGameplayEffects        | Display                 |
| LogGameplayTags           | Log                     |
| LogGameplayTasks          | Log                     |
| VLogAbilitySystem         | Display                 |

See the [Wiki on Logging](https://www.ue4community.wiki/Legacy/Logs,_Printing_Messages_To_Yourself_During_Runtime) for more information.

**[⬆ Back to Top](#table-of-contents)**

<a name="optimizations"></a>
## 7. Optimizations

<a name="optimizations-abilitybatching"></a>
### 7.1 Ability Batching
[`GameplayAbilities`](#concepts-ga) that activate, optionally send `TargetData` to the server, and end all in one frame can be [batched to condense two-three RPCs into one RPC](#concepts-ga-batching). These types of abilities are commonly used for hitscan guns.

<a name="optimizations-gameplaycuebatching"></a>
### 7.2 Gameplay Cue Batching
If you're sending many [`GameplayCues`](#concepts-gc) at the same time, consider [batching them into one RPC](#concepts-gc-batching). The goal is to reduce the number of RPCs (`GameplayCues` are unreliable NetMulticasts) and send as little data as possible.

<a name="optimizations-ascreplicationmode"></a>
### 7.3 AbilitySystemComponent Replication Mode
By default, the [`ASC`](#concepts-asc) is in [`Full Replication Mode`](#concepts-asc-rm). This will replicate all [`GameplayEffects`](#concepts-ge) to every client (which is fine for a single player game). In a multiplayer game, set the player owned `ASCs` to `Mixed Replication Mode` and AI controlled characters to `Minimal Replication Mode`. This will replicate `GEs` applied on a player character to only replicate to the owner of that character and `GEs` applied on AI controlled characters will never replicate `GEs` to clients. [`GameplayTags`](#concepts-gt) will still replicate and [`GameplayCues`](#concepts-gc) will still unreliable NetMulticast to all clients, regardless of the `Replication Mode`. This will cut down on network data from `GEs` being replicated when all clients don't need to see them.

<a name="optimizations-attributeproxyreplication"></a>
### 7.4 Attribute Proxy Replication
In large games with many players like Fortnite Battle Royale (FNBR), there will be a lot of [`ASCs`](#concepts-asc) living on always-relevant `PlayerStates` replicating a lot of [`Attributes`](#concepts-a). To optimize this bottleneck, Fortnite disables the `ASC` and its [`AttributeSets`](#concepts-as) from replicating altogether on **simulated player-controlled proxies** in the `PlayerState::ReplicateSubobjects()`. Autonomous proxies and AI controlled `Pawns` still fully replicate according to their [`Replication Mode`](#concepts-asc-rm). Instead of replicating `Attributes` on the `ASC` on the always-relevant `PlayerStates`, FNBR uses a replicated proxy structure on the player's `Pawn`. When `Attributes` change on the server's `ASC`, they are changed on the proxy struct too. The client receives the replicated `Attributes` from the proxy struct and pushes the changes back into its local `ASC`. This allows `Attribute` replication to use the `Pawn`'s relevancy and `NetUpdateFrequency`. This proxy struct also replicates a small white-listed set of `GameplayTags` in a bitmask. This optimization reduces the amount of data over the network and allows us to take advantage of pawn relevancy. AI controlled `Pawns` have their `ASC` on the `Pawn` which already uses its relevancy so this optimization is not needed for them.

> I’m not sure if it is still necessary with other server side optimizations that have been done since then (Replication Graph, etc) and it is not the most maintainable pattern.

*Dave Ratti from Epic's answer to [community questions #3](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)*

<a name="optimizations-asclazyloading"></a>
### 7.5 ASC Lazy Loading
Fortnite Battle Royale (FNBR) has a lot of damageable `AActors` (trees, buildings, etc) in the world, each with an [`ASC`](#concepts-asc). This can add up in memory cost. FNBR optimizes this by lazily loading `ASCs` only when they're needed (when they first take damage by a player). This reduces overall memory usage since some `AActors` may never be damaged in a match.

**[⬆ Back to Top](#table-of-contents)**

<a name="qol"></a>
## 8. Quality of Life Suggestions

<a name="qol-gameplayeffectcontainers"></a>
### 8.1 Gameplay Effect Containers
[GameplayEffectContainers](#concepts-ge-containers) combine [`GameplayEffectSpecs`](#concepts-ge-spec), [`TargetData`](#concepts-targeting-data), [simple targeting](#concepts-targeting-containers), and related functionality into easy to use structures. These are great for transfering `GameplayEffectSpecs` to projectiles spawned from an ability that will then apply them on collision at a later time.

<a name="qol-asynctasksascdelegates"></a>
### 8.2 Blueprint AsyncTasks to Bind to ASC Delegates
To increase designer-friendly iteration times, especially when designing UMG Widgets for UI, create Blueprint AsyncTasks (in C++) to bind to the common change delegates on the `ASC` directly from your UMG Blueprint graphs. The only caveat is that they must be manually destroyed (like when the widget is destroyed) otherwise they will live in memory forever. The Sample Project includes three Blueprint AsyncTasks.

Listen for `Attribute` changes:

![Listen for Attributes Changes BP Node](https://github.com/dohi/GASDocumentation/raw/master/Images/attributeschange.png)

Listen for cooldown changes:

![Listen for Cooldown Change BP Node](https://github.com/dohi/GASDocumentation/raw/master/Images/cooldownchange.png)

Listen for `GE` stack changes:

![Listen for GameplayEffect Stack Change BP Node](https://github.com/dohi/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting"></a>
## 9. Troubleshooting

<a name="troubleshooting-notlocal"></a>
### 9.1 `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`
You need to [initialize the `ASC` on the client](#concepts-asc-setup).

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-scriptstructcache"></a>
### 9.2 `ScriptStructCache` errors
You need to call [`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata).

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-replicatinganimmontages"></a>
### 9.3 Animation Montages are not replicating to clients
Make sure that you're using the `PlayMontageAndWait` Blueprint node instead of `PlayMontage` in your [GameplayAbilities](#concepts-ga). This [AbilityTask](#concepts-at) replicates the montage through the `ASC` automatically whereas the `PlayMontage` node does not.

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-duplicatingblueprintactors"></a>
### 9.4 Duplicating Blueprint Actors is setting AttributeSets to nullptr
There is a [bug in Unreal Engine](https://issues.unrealengine.com/issue/UE-81109) that will set `AttributeSet` pointers on your classes to nullptr for Blueprint Actor classes that are duplicated from existing Blueprint Actor classes. There are a few workarounds for this. I've had success not creating bespoke `AttributeSet` pointers on my classes (no pointer in the .h, not calling `CreateDefaultSubobject` in the constructor) and instead just directly adding `AttributeSets` to the `ASC` in `PostInitializeComponents()` (not shown in the Sample Project). The replicated `AttributeSets` will still live in the `ASC's` `SpawnedAttributes` array. It would look something like this:

```c++
void AGDPlayerState::PostInitializeComponents()
{
	Super::PostInitializeComponents();

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->AddSet<UGDAttributeSetBase>();
		// ... any other AttributeSets that you may have
	}
}
```

In this scenario, you would read and set the values in the `AttributeSet` using the functions on the `ASC` instead of [calling functions on the `AttributeSet` made from the macros](#concepts-as-attributes).

```c++
/** Returns current (final) value of an attribute */
float GetNumericAttribute(const FGameplayAttribute &Attribute) const;

/** Sets the base value of an attribute. Existing active modifiers are NOT cleared and will act upon the new base value. */
void SetNumericAttributeBase(const FGameplayAttribute &Attribute, float NewBaseValue);
```

So the `GetHealth()` would look something like:

```c++
float AGDPlayerState::GetHealth() const
{
	if (AbilitySystemComponent)
	{
		return AbilitySystemComponent->GetNumericAttribute(UGDAttributeSetBase::GetHealthAttribute());
	}

	return 0.0f;
}
```

Setting (initializing) the health `Attribute` would look something like:

```c++
const float NewHealth = 100.0f;
if (AbilitySystemComponent)
{
	AbilitySystemComponent->SetNumericAttributeBase(UGDAttributeSetBase::GetHealthAttribute(), NewHealth);
}
```

As a reminder, the `ASC` only ever expects at most one `AttributeSet` object per `AttributeSet` class.

**[⬆ Back to Top](#table-of-contents)**

<a name="acronyms"></a>
## 10. Common GAS Acronyms

| Name                                                                                                   | Acronyms            |
|------------------------------------------------------------------------------------------------------- | ------------------- |
| AbilitySystemComponent                                                                                 | ASC                 |
| AbilityTask                                                                                            | AT                  |
| [Action RPG Sample Project by Epic](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) | ARPG, ARPG Sample   |
| CharacterMovementComponent                                                                             | CMC                 |
| GameplayAbility                                                                                        | GA                  |
| GameplayAbilitySystem                                                                                  | GAS                 |
| GameplayCue                                                                                            | GC                  |
| GameplayEffect                                                                                         | GE                  |
| GameplayEffectExecutionCalculation                                                                     | ExecCalc, Execution |
| GameplayTag                                                                                            | Tag, GT             |
| ModifierMagnitudeCalculation                                                                           | ModMagCalc, MMC     |

**[⬆ Back to Top](#table-of-contents)**

<a name="resources"></a>
## 11. Other Resources
* [Official Documentation](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html)
* Source Code!
   * Especially `GameplayPrediction.h`
* [Action RPG Sample Project by Epic](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)
* [Unreal Slackers Discord](https://unrealslackers.org/) has a text channel dedicated to GAS `#gameplay-ability-system`
   * Check pinned messages
* [GitHub repository of resources by Dan 'Pan'](https://github.com/Pantong51/GASContent)
* [YouTube Videos by SabreDartStudios](https://www.youtube.com/channel/UCCFUhQ6xQyjXDZ_d6X_H_-A)

<a name="resources-daveratti"></a>
### 11.1 Q&A With Epic Game's Dave Ratti

<a name="resources-daveratti-community1"></a>
#### 11.1.1 Community Questions 1
[Dave Ratti responses to the Unreal Slackers Discord Server community questions about GAS](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89):

1. How can we create scoped prediction windows on demand outside or irrespective of GameplayAbilities? For example, how can a fire and forget projectile locally predict a damage GameplayEffectwhen it hits an enemy?

> The PredictionKey system is not really meant to do this. Fundamentally this systems works by a client initiating a predictive action, telling the server about it with a key, and then both client and server running the same thing and associating predictive side effects with the given prediction key. For example, “I am predictively activating an ability” or “I have produced target data and am going to predictively run the part of the ability graph after the WaitTargetData task”.
>
> With this pattern, the PredictionKey “bounces” off the server and comes back to the client via UAbilitySystemComponent::ReplicatedPredictionKeyMap (replicated property). Once the key is replicated back from the server, the client is able to undo all of the locally predictive side effects (GameplayCues, GameplayEffects): the replicated versions *will be there* and if they aren’t then it was a misprediction. Knowing exactly when to undo the predictive side effects is crucial here: if you are too early you will see gaps, if you are too late you will have “double”. (Note this is referring to stateful prediction, like a looping GameplayCue of a duration based Gameplay Effect. “Burst” GameplayCues and instant Gameplay Effects are never “undone” or rolled back. They are just skipped on the client if there is a prediction key associated with them).
>
> To further hit home the point: it’s crucial that predictive action is something the server does not do on their own, but only does so when the client tells them too. So having a generic “Create a key on demand and tell the server so I can run something” does not work unless that “something” is something the server will only do once told to by the client.
>
> Backing up to the original question: something like a fire and forget projectile. Both Paragon and Fornite have projectile actor classes that use GameplayCues. However we do not use the Prediction Key system to do these. Instead we have a concept on Non-Replicated GameplayCues. GameplayCues that just fire off locally and are skipped by the server completely.  Essentially all these are, are direct calls to UGameplayCueManager::HandleGameplayCue. They do not route through the UAbilitySystemComponent so no prediction key checks / early returns are made.
>
> The downside with non replicated GameplayCues is that, well, they are not replicated. So its up to the projectile class/blueprint to make sure the code paths that call these functions are running on everyone. We have for cues startup (called in BeginPlay), explosion, hit wall/character, etc.
>
> These type ofevents are already generated client side, so calling into a non replicated gameplay cue was no big deal. Complicated blueprints can be tricky, and are up to the author to make sure they understand what is running where.


2. When using a WaitNetSyncAbilityTaskwith OnlyServerWaitto create a scoped prediction window in a locally predicted GameplayAbility, could players potentially cheat by delaying their packets to the Server to control GameplayAbilitytiming since the Server is waiting for their RPC withtheir prediction key? Was this ever an issue in Paragon or Fortnite, and if so, what did Epic do to remedy it?

> Yes, this is a valid concern. Any ability blueprint running on the server that is waiting for a client “signal” is potentially vulnerable to lag switch type exploits.
>
> Paragon had a custom targeting task similar to UAbilityTask_WaitTargetData. In this task we had timeouts, or a “max delay” that we would wait on the client for instantaneous targeting modes. If the targeting mode was waiting for user confirmation (button press) then it would be ignored since the user is allowed to take his time. But for abilities that instantly confirmed targeting we would only wait a certain amount of time before either A) generating the target data server side of B) canceling the ability.
>
> We never had such mechanisms for WaitNetSync, which we used pretty sparingly.
>
> I don’t believe Fortnite makes use of anything like this though. The weapon abilities in Fortnite are special cased batched to a single fortnite-specific RPC: one RPC to activate the ability, provide target data, and end the ability. So weapon abilities are intrinsically not vulnerable to this in Battle Royale.
>
> My take is that this is something that could probably be solved system wide but I don’t see us making the change ourselves anytime soon. Spot fixing WaitNetSync to include a max delay for the case you mention is probably a reasonable task, but again - unlikely we will do this on our end in the immediate future.


3. Which EGameplayEffectReplicationModedid Paragon and Fortnite use and what are Epic’s recommendations for when to use each?

> Both games essentially use Mixed mode for their player controlled characters and Minimal for AI controlled (AI minions, jungle creeps, AIHusks, etc). This is what I would recommend most people using the system in a multiplayer game. The sooner into your project you set these, the better.
>
> Fortnite goes a few steps further with its optimizations. It actually does not replicate the UAbilitySystemComponent at all for simulated proxies. The component and attribute subobjects are skipped inside ::ReplicateSubobjects() on the owning fortnite player state class. We do push the bare minimum replicated data from the ability system component to a structure on the pawn itself (basically, a subset of attribute values and a white list subset of tags that we replicate down in a bitmask). We call this a “proxy”. On the receiving side we take the proxy data, replicated on the pawn, and push it back into ability system component on the player state. So you do have an ASC for each player in FNBR, it just doesn’t directly replicate: instead it replicates data via a minimal proxy struct on the pawn and then routes back to the ASC on receiving side. This is advantage since its A) a more minimal set of data B) takes advantage of pawn relevancy.
>
> I’m not sure if it is still necessary with other server side optimizations that have been done since then (Replication Graph, etc) and it is not the most maintainable pattern.


4. Since we cannot predict the removal of GameplayEffectsas per GameplayPrediction.h, are there any strategies for mitigating the effects of latency on removing GameplayEffects? For example, when removing a movement speed slow, we currently have to wait for the Server to replicate the GameplayEffectremoval resulting in a snap of the player’s character position.

> This is a tough one and I don’t have a good answer. We generally skirted around these problems with tolerances and smoothing. I totally agree that ability system and precise synchronization with the character movement system is not in a good place and something we do want to fix.
>
> I had a shelf of allowing predictive removal of GEs but could never work out all edge cases before having to move on. This doesn’t solve everything though since character movement still has an internal saved move buffer that does not know anything about the ability system and possible movement speed modifiers, etc. It is still possible to get into correction feedback loops even outside of not being able to predict the removal of GEs.
>
> If you think you have a case that is truly desperate, you are able to predictively add a GE that would inhibit your movement speed GEs. I’ve never done this myself but have theorized about it before. It may be able to help with a certain class of problem.


5. We know that the AbilitySystemComponentlives on the PlayerStatein Paragon and Fortnite and on the Characterin the Action RPG Sample. What are Epic’s internal rules, guidelines, or recommendations for where the AbilitySystemComponentshould live --what should its Ownerbe?

> In general I would say anything that does not need to respawn should have the Owner andAvatar actor be the same thing. Anything like AI enemies, buildings, world props, etc.
>
> Anything that does respawn should have the Owner and Avatar be different so that the Ability System Component does not need to be saved off / recreated / restored after a respawn.. PlayerState is the logical choice it is replicated to all clients (where as PlayerController is not). The downside is PlayerStates are always relevant so you can run into problems in 100 player games. (See notes on what FN did in question #3).


6. Is it viable to have several AbilitySystemComponentswhich have the same owner but different avatars (e.g. on pawn and weapon/items/projectiles with Ownerset to PlayerState)?

> The first problem I see there would be implementing the IGameplayTagAssetInterface and IAbilitySystemInterface on the owning actor. The former may be possible: just aggregate the tags from all all ASCs (but watch out - HasAlMatchingGameplayTags may be met only via cross ASC aggregation. It wouldn't be enough to just forward that calls to each ASC and OR the results together). But the later is even trickier: which ASC is the authoritative one? If someone wants to apply a GE - which one should receive it? Maybe you can work these out but this side of the problem will be the hardest: owners will multiple ASCs beneath them.
>
> Separate ASCs on the pawn and the weapon can make sense on its own though. E.g, distinguishing between tags the describe the weapon vs those that describe the owning pawn. Maybe it does make sense that tags granted to the weapon also “apply” to the owner and nothing else (E.g, attributes and GEs are independent but the owner will aggregate the owned tags like I describe above). This could work out, I am sure. But having multiple ASCs with the same owner may get dicey.


7. Is there a way to stop the Server from overwriting the cooldown duration of locally predicted abilities on the Owning Client? In scenarios of high latency, this would let theOwning Client "try" to activate the ability again when its local cooldown expires but it is still on cooldown on the Server. By the time the Owning Client's activation request reaches the Server over the network, the Server may be off cooldown or the Server might be able to queue the activation request for the remaining milliseconds that it has left. Otherwise as is, clients with higher latency have a longer delay before when they can reactivate an ability versus those with less latency. This is most apparent with very low cooldown abilities like a basic attack that can be less than one second of cooldown. If there isn't a way to stop the Server from overwriting the cooldown duration of locally predicted abilities, what is Epic's strategy for mitigating theeffects of high latency on reactivating abilities? To word it another example-based way, how did Epic design Paragon's basic attacks and other abilities so that high latency players could attack or activate at the same speed as low latency players with local prediction?

> The short answer there is not a way to prevent this and Paragon definitely had the problem. Higher latency connections would have a lower ROF with basic attacks.
>
> I attempted to fix this by adding “GE reconciliation” where latency was taken into account when calculating GE duration. Essentially allowing the server to eat some of the total GE time so that the effective time of the GE client side would be 100% consistent with any amount of latency (though fluctuations could still cause issues). However I never got this working in a state that could ship and the project moved fast and we just never fully addressed it.
>
> Fortnite does its own bookkeeping for weapon firing rates: it does not use GEs for cooldowns on weapons. I would recommend this if this is a critical problem for your game.


8. What is Epic’s roadmap for the GameplayAbilitySystem plugin? Which features does Epic plan to add in 2019 and beyond?

> We feel that overall the system is pretty stable at this point and we don’t have anyone working on major new features. Bug fixes and small improvements occasionally are made for Fortnite or from UDN/pull requests, but that is it right now.
>
> Longer term, I think we will eventually do a “V2” or some big changes. We learned a lot from writing this system and feel we got a lot right and a lot wrong. I would love a chance to correct those mistakes and improve some of the fatal flaws that were pointed out above.
>
> If a V2 was to ever come, providing an upgrade path would be of utmost importance. We would never make a V2 and leave Fortnite on V1 forever: there would be some path or procedures that would automatically migrate as much as possible, though there would still almost certainly be some manual remaking required.
>
> The high priority fixes would be:
> * Better interoperability with the character movement system. Unifying client prediction.
> * GE removal prediction (question #4)
> * GE latency reconciliation (question #8)
> * Generalized network optimizations such as batching RPCs and proxy structures. Mostly the stuff that we’ve done for Fortnite but find ways to break it down into more generalized form, at least so that games can write their own game specific optimizations more easily.
>
> The more general refactor type of changes I would consider making:
> * I would like to look at fundamentally moving away from having GEs reference spreadsheet values directly, instead they would be able to emit parameters and those parameters could be filled by some higher level object that is bound to spreadsheet values. The problem with the current model is that GEs become unsharable due to their tight coupling with the curve table rows. I think a generalized system for parameterization could be written and be the underpinning of a V2 system.
> * Reduce number of “policies” on UGameplayAbility. I would remove ReplicationPolicy InstancingPolicy. Replication is, imo, almost never actually needed and causes confusion. InstancingPolicy should be replaced instead by making FGameplayAbilitySpec a UObject that can be subclassed. This should have been the “non instantiated ability object” that has events and is blueprintable. The UGameplayAbility should be the “instanced per execution” object. It could be optional if you need to actually instantiate: instead “non instanced” abilities would be implemented via the new UGameplayAbilitySpec object. 
> * The system should provide more “middle level” constructs such as “filtered GE application container” (data drive what GEs to apply to which actors with higher level gameplay logic), “Overlapping volume support” (apply the “Filtered GE application container” based on collision primitive overlap events), etc.These are building blocks that every project ends up implementing in their own way. Getting them right is non trivial so I think we should do a better job providing some basic implementations. 
> * In general, reducing boilerplate needed to get your project up and running. Possibly a separate module “Ex library” or whatever that could provide things like passive abilities or basichitscan weapons out of the box. This module would be optional but would get you up and running quickly.
> * I would like to move GameplayCues to a separate module that is not coupled with the ability system. I think there are a lot of improvements that could be made here.


> This is only my personal opinion and not a commitment from anyone. I think the most realistic course of action will be as new engine tech initiatives come through, the ability system will need to be updated and that will be a time to do this sort of thing. These initiatives could be related to scripting, networking, or physics/character movement. This is all very far looking ahead though so I cannot give commitments or estimates on timelines.

**[⬆ Back to Top](#table-of-contents)**

<a name="resources-daveratti-community2"></a>
#### 11.1.2 Community Questions 2
Community member [iniside](https://github.com/iniside)'s Q&A with Dave Ratti:

1. Is the support for decoupled fixed ticking planned ? I'd like to
have Game Thread be fixed (like 30/60fps) and let the rendering thread
run wild. I ask if this is something we should expect in future or
not, to make some assumptions about how gameplay should work.
I ask mainly because there is now a fixed async tick for physics and
this poses a question how the rest of the system might work in the
future. I do not hide that having the ability to have fixed tick game
thread without also fixing tick rate of the rest of the engine would
be beyond awesome.

> There are no plans to decouple rendering frame rate and game thread tick frame rate. I think the ship has sailed on this ever happening due to the complexity of these systems and the requirement to preserve backwards compatibility with previous engine versions.
>
> Instead, the direction we've gone is to have an asynchronous "Physics Thread" which runs at a fixed tick rate, independent of the game thread. Things that need to run at a fixed rate can run here and the game thread / rendering can operate how they always have.
>
> It's worth clarifying that Network Prediction supports what it calls Independent Ticking and Fixed Ticking modes. My long term plan is to keep Independent Ticking roughly how it is today in Network Prediction where it runs on the game thread at variable frame rate and there is no "group/world" prediction, it's just the classic "clients predict their own pawn and owned actors" model. And Fixed Ticking would be what uses the async physics stuff and allows you to predict non client controlled/owned actors like physics objects and other clients/pawns/vehicles/etc.


2. Is there any plan on how the integration of Network Prediction will
look with the Ability System ? Like for example, fixed frame ability
activation (so the server gets frames in which abilities were
activated and tasks executed instead of prediction keys) ?

> Yes, the plan is to rewrite/remove the Ability System's prediction keys and replace them with Network Prediction constructs. The MockAbility examples in NetworkPredictionExtras show how this might work but they are more "hard coded" than what GAS will require. 
>
> The main idea would be that we remove the explicit client->server Prediction Key exchange in the ASC's RPCs. There would no longer be prediction windows or scoped prediction keys. Instead everything would be anchored around NetworkPrediction frames. The important thing is that client and server agree on when things happen. Examples would be:
>
> * When abilities were activated/ended/cancelled
> * When Gameplay Effects were applied/removed
> * Attribute values (what an attributes value was at frame X)
>
> I think this could be done generically at the ability system level. But actually making the user-defined logic inside a UGameplayAbility completely rollback-able would still take more work. We may end up having a subclass of UGameplayAbility that is fully rollbackable and has access to a more limited set of functionality or only Ability Tasks that are marked as rollback-friendly. Something like that. There are also many implications to animation events and root motion and how those are processed.
>
> Wish I had a more clear answer but it's really important we get the foundation right before touching GAS again. Movement and physics have to be solid before the higher level systems can be changed.


3. Is there a plan to move Network Prediction development toward the
main branch ? Not gonna lie, I'd really like to check the latest code.
Regardless of it's state.

> We are working towards it. The system work is still all being done in NetworkPrediction (see NetworkPhysics.h) and the underlying async physics stuff should be all available (RewindData.h etc). But we also have use cases in Fortnite that we have been focused on that obviously can't be made public. We are working through bugs, performance optimizations, etc.
>
> For more context: when working on the early versions of this system, we were very focused on the "front end" of things - how state and simulations were defined and written. We learned a lot there. But as the async physics stuff has come online, we've been much more focused on just getting something real to work in this system, at the expense of throwing out some of our early abstractions. The goal here is to circle back when the real thing is working and reunifying things. E.g, get back to the "front end" and make the final version of that on top of the core pieces of tech we are working on now.


4. For some time on Main there was a plugin for sending Gameplay
Messages (Looked like Event/Message Bus), but it was removed. Any
plans to restore it ? With the Game Features/Modular Gameplay plugins,
having a generic Event Bus Dispatcher would be extremely useful.

> I think you are referring to the GameplayMessages plugin. This will probably come back at some point - the API isn't really finalized yet and the author didn't mean for it to be public yet. I agree it should be useful for modular gameplay design. But it's not really my area so I don't have much more information. 


5. I've been playing recently with async fixed physics and the results
are promising, though if there is going to be NP update in the future
I will probably just play around and wait, since to get it working I
still need to get entire engine into fixed tick and on the other hand
I try to keep physics at 33ms. Which does not make for a good
experience if everything is at 30 fps (:.

I have noticed there was some work on Async
CharacterMovementComponent, but not sure if this will be using Network
Prediction, or it is a separate effort ?

Since I noticed it, I also went ahead and tried to implement my custom
async movement at fixed tick rate, which worked okay, but on top of it
I also needed to add a separate update for interpolation. The setup
was to run simulation tick on separate worker threads at fixed 33ms
update, do calculations, save result, and interpolate it at the game
thread to match current frame rate. Not perfect, but it got the job
done.

My question is, if this is something that might be easier to set up in
the future, as there is just quite a bit of boilerplate code to write,
(the interpolation part) and it's not particularly efficient to
interpolate each moving object individually.

The async stuff is really interesting, because it would allow you to
really run game simulation at fixed update rate (which would make
fixed thread unneeded) and have more predictable results. Is this
something that is intended going forward, or more of a benefit to
select systems ? As far as I remember actor transforms are not updated
async and blueprints are not entirely thread safe. In other words is
it something that is planned to be supported at more of a framework
level or something that each game has to solve on it's own ?

> Async CharacterMovementComponent
>
> This is basically an early prototype/experiment of porting CMC as it is to the physics thread. I don't view it as the future of CMC yet, but it could evolve into that. Right now there is no networking support so it's not something I would really follow. The people doing it are mostly concerned with measuring input latency that this system would add and how that could be mitigated.
>
> I still need to get entire engine into fixed tick and on the other hand I try to keep physics at 33ms. Which does not make for a good experience if everything is at 30 fps (:.
>
> The async stuff is really interesting, because it would allow you to really run game simulation at fixed update rate (which would make fixed thread unneeded) 
>
> Yes. The goal here is that with async physics enabled, you can run the engine at variable tick rate while the physics and "core" gameplay simulations can run at the fixed rate (such as character movement, vehicles, GAS, etc).
>
> These are the cvars that need to be set to enable this now: (I think you've figured this out)  
> `p.DefaultAsyncDt=0.03333`  
> `p.RewindCaptureNumFrames=64`
>
> Chaos does provide interpolation for the physics state (E.g, the transforms that get pushed back to the UPrimitiveComponent and are visible to the game code). There is a cvar now, `p.AsyncInterpolationMultiplier`, which controls that if you want to look at it. You should see smooth continuous motion of physics bodies without having to write any extra code. 
>
> If you want to interpolate non physics state, it is still up to you to do that right now. The example would be like a cool-down that you want to update (tick) on the async physics thread but see smooth continuous interpolation on the game thread so that every render frame the cool down visualization is updated. We will get to this eventually but don't have examples yet.
>
> there is just quite a bit of boilerplate code to write,
>
> Yeah, so that has been a big general problem with the system up until now. We want to provide an interface that experienced programmers can use to maximize performance and safety (the ability to write gameplay code that "just works" predictively without tons of hazards and things you could-do-but-better-not). So something like CharacterMoverment might do a bunch of custom stuff to maximize its performance - e.g, writing templated code and doing batch updating, going wide, breaking the update loop into distinct phases etc. We want to provide a good "low level" interface into the async thread and rollback systems for this use case. And in this case too - it's still reasonable that the character movement system itself is extendable in its own way. For example providing a way to blueprint a custom movement mode and providing a blueprint API that is thread safe.
>
> But we recognize this is not acceptable for simpler gameplay objects that don't really need their own "system". Something more inline with Unreal is what is needed. E.g, using the reflection system, having general blueprint support, etc. There are examples of blueprints being used on other threads (see BlueprintThreadSafe keyword and what the animation system has been working towards). So I think there will be some form of this one day. But again, we aren't there yet.
>
> I realize you were just asking about interpolation but that is the general answer: right now we have you do everything manually like NetSerialize, ShouldReconcile, Interpolate, etc but eventually we'll have a way that is like "if you want to just use the reflection system, you don't have to manually write this stuff". We just don't want to *force* everyone to use the reflection system since that imposes other limitations that we think we don't want to take on the lowest levels of the system. 
>
> And then just to tie this back to what I said earlier - right now we are really focused on getting a few very specific examples working and performant and then we will turn attention back to the front end and making things friendly to use and iterate on, reducing boilerplate, etc for everybody else to use. 

**[⬆ Back to Top](#table-of-contents)**

<a name="changelog"></a>
## 12. GAS Changelog

This is a list of notable changes (fixes, changes, and new features) to GAS compiled from the official Unreal Engine upgrade changelog and from undocumented changes that I've encountered. If you've found something that isn't listed here, please make an issue or pull request.

<a name="changelog-4.26"></a>
### 4.26
* GAS plugin is no longer flagged as beta.
* Crash Fix: Fixed a crash when adding a gameplay tag without a valid tag source selection.
* Crash Fix: Added the path string arg to a message to fix a crash in UGameplayCueManager::VerifyNotifyAssetIsInValidPath.
* Crash Fix: Fixed an access violation crash in AbilitySystemComponent_Abilities when using a ptr without checking it.
* Bug Fix: Fixed a bug where stacking GEs that did not reset the duration on additional instances of the effect being applied.
* Bug Fix: Fixed an issue that caused CancelAllAbilities to only cancel non-instanced abilities.
* New: Added optional tag parameters to gameplay ability commit functions.
* New: Added StartTimeSeconds to PlayMontageAndWait ability task and improved comments.
* New: Added tag container "DynamicAbilityTags" to FGameplayAbilitySpec. These are optional ability tags that are replicated with the spec. They are also captured as source tags by applied gameplay effects.
* New: GameplayAbility IsLocallyControlled and HasAuthority functions are now callable from Blueprint.
* New: Visual logger will now only collect and store info about instant GEs if we're currently recording visual logging data.
* New: Added support for redirectors on gameplay attribute pins in blueprint nodes.
* New: Added new functionality for when root motion movement related ability tasks end they will return the movement component's movement mode to the movement mode it was in before the task started.

<a name="changelog-4.25.1"></a>
### 4.25.1
* Fixed! UE-92787 Crash saving blueprint with a Get Float Attribute node and the attribute pin is set inline
* Fixed! UE-92810 Crash spawning actor with instance editable gameplay tag property that was changed inline

<a name="changelog-4.25"></a>
### 4.25
* Fixed prediction of `RootMotionSource` `AbilityTasks`
* [`GAMEPLAYATTRIBUTE_REPNOTIFY()`](#concepts-as-attributes) now additionally takes in the old `Attribute` value. We must supply that as the optional parameter to our `OnRep` functions. Previously, it was reading the attribute value to try to get the old value. However, if called from a replication function, the old value had already been discarded before reaching SetBaseAttributeValueFromReplication so we'd get the new value instead.
* Added [`NetSecurityPolicy`](#concepts-ga-netsecuritypolicy) to `UGameplayAbility`.
* Crash Fix: Fixed a crash when adding a gameplay tag without a valid tag source selection.
* Crash Fix: Removed a few ways for attackers to crash a server through the ability system.
* Crash Fix: We now make sure we have a GameplayEffect definition before checking tag requirements.
* Bug Fix: Fixed an issue with gameplay tag categories not applying to function parameters in Blueprints if they were part of a function terminator node.
* Bug Fix: Fixed an issue with gameplay effects' tags not being replicated with multiple viewports.
* Bug Fix: Fixed a bug where a gameplay ability spec could be invalidated by the InternalTryActivateAbility function while looping through triggered abilities.
* Bug Fix: Changed how we handle updating gameplay tags inside of tag count containers. When deferring the update of parent tags while removing gameplay tags, we will now call the change-related delegates after the parent tags have updated. This ensures that the tag table is in a consistent state when the delegates broadcast.
* Bug Fix: We now make a copy of the spawned target actor array before iterating over it inside when confirming targets because some callbacks may modify the array.
* Bug Fix: Fixed a bug where stacking GameplayEffects that did not reset the duration on additional instances of the effect being applied and with set by caller durations would only have the duration correctly set for the first instance on the stack. All other GE specs in the stack would have a duration of 1 second. Added automation tests to detect this case.
* Bug Fix: Fixed a bug that could occur if handling gameplay event delegates modified the list of gameplay event delegates.
* Bug Fix: Fixed a bug causing GiveAbilityAndActivateOnce to behave inconsistently.
* Bug Fix: Reordered some operations inside FGameplayEffectSpec::Initialize to deal with a potential ordering dependency.
* New: UGameplayAbility now has an OnRemoveAbility function. It follows the same pattern as OnGiveAbility and is only called on the primary instance of the ability or the class default object.
* New: When displaying blocked ability tags, the debug text now includes the total number of blocked tags.
* New: Renamed UAbilitySystemComponent::InternalServerTryActiveAbility to UAbilitySystemComponent::InternalServerTryActivateAbility.Code that was calling InternalServerTryActiveAbility should now call InternalServerTryActivateAbility.
* New: Continue to use the filter text for displaying gameplay tags when a tag is added or deleted. The previous behavior cleared the filter.
* New: Don't reset the tag source when we add a new tag in the editor.
* New: Added the ability to query an ability system component for all active gameplay effects that have a specified set of tags. The new function is called GetActiveEffectsWithAllTags and can be accessed through code or blueprints.
* New: When root motion movement related ability tasks end they now return the movement component's movement mode to the movement mode it was in before the task started.
* New: Made SpawnedAttributes transient so it won't save data that can become stale and incorrect. Added null checks to prevent any currently saved stale data from propagating. This prevents problems related to bad data getting stored in SpawnedAttributes.
* API Change: AddDefaultSubobjectSet has been deprecated. AddAttributeSetSubobject should be used instead.
* New: Gameplay Abilities can now specify the Anim Instance on which to play a montage.

<a name="changelog-4.24"></a>
### 4.24
* Fixed blueprint node `Attribute` variables resetting to `None` on compile.
* Need to call [`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata) to use [`TargetData`](#concepts-targeting-data) otherwise you will get `ScriptStructCache` errors and clients will be disconnected from the server. My advice is to always call this in every project now whereas before 4.24 it was optional.
* Fixed crash when copying a `GameplayTag` setter to a blueprint that didn't have the variable previously defined.
* `UGameplayAbility::MontageStop()` function now properly uses the `OverrideBlendOutTime` parameter.
* Fixed `GameplayTag` query variables on components not being modified when edited.
* Added the ability for `GameplayEffectExecutionCalculations` to support scoped modifiers against "temporary variables" that aren't required to be backed by an attribute capture.
   * Implementation basically enables `GameplayTag`-identified aggregators to be created as a means for an execution to expose a temporary value to be manipulated with scoped modifiers; you can now build formulas that want manipulatable values that don't need to be captured from a source or target.
   * To use, an execution has to add a tag to the new member variable `ValidTransientAggregatorIdentifiers`; those tags will show up in the calculation modifier array of scoped mods at the bottom, marked as temporary variables—with updated details customizations accordingly to support feature
* Added restricted tag quality-of-life improvements. Removed the default option for restricted `GameplayTag` source. We no longer reset the source when adding restricted tags to make it easier to add several in a row. 
* `APawn::PossessedBy()` now sets the owner of the `Pawn` to the new `Controller`. Useful because [Mixed Replication Mode](#concepts-asc-rm) expects the owner of the `Pawn` to be the `Controller` if the `ASC` lives on the `Pawn`.
* Fixed bug with POD (Plain Old Data) in `FAttributeSetInitterDiscreteLevels`.

**[⬆ Back to Top](#table-of-contents)**
