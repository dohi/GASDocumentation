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
### 4.7 Ability Tasks: アビリティタスク

<a name="concepts-at-definition"></a>
### 4.7.1 アビリティタスクの定義
`GameplayAbilities`は1フレーム内でのみ実行されます。これだけではあまり柔軟性がありません。時間をかけて行われるアクションや、後から発射されるデリゲートへの対応を必要とするアクションを行うには、`AbilityTasks`と呼ばれる潜在的なアクション（latent actions）を使用します。

GASには多くの `AbilityTasks` が付属しています。
* `RootMotionSource`を使ってキャラクターを動かすためのタスク。
* アニメーションモンタージュを再生するタスク
* `Attribute` の変更に応答するためのタスク
* `GameplayEffect`の変更に対応するタスク
* プレイヤーの入力に応答するためのタスク
* などなど

`UAbilityTask`のコンストラクタでは、ゲーム全体で同時実行可能な`AbilityTask`の最大数が1000個というハードコードが適用されます。RTSゲームのように何百人ものキャラクターが同時に登場するようなゲームで`GameplayAbilities`を設計する際には、この点に注意してください。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-definition"></a>
### 4.7.2 カスタムアビリティータスク
多くの場合、独自のカスタム`AbilityTasks`を（C++で）作成することになります。サンプルプロジェクトには2つのカスタム`AbilityTasks`が付属しています。
1. `PlayMontageAndWaitForEvent` は、デフォルトの `PlayMontageAndWait` と `WaitGameplayEvent` `AbilityTasks` を組み合わせたものです。これにより、アニメーション・モンタージュは、ゲームプレイ・イベントを `AnimNotifies` から開始した `GameplayAbility` に送り返すことができます。アニメーション・モンタージュ中の特定の時間にアクションを起こすために使用します。
1. `WaitReceiveDamage`は`OwnerActor`がダメージを受けるのを待ちます。パッシブアーマースタック `GameplayAbility` は、ヒーローがダメージを受けたときにアーマーのスタックを取り除きます。

`AbilityTasks` は次のように構成されています。

* `AbilityTask` の新しいインスタンスを作成するスタティック関数。
* `AbilityTask` が目的を達成したときにブロードキャストされるデリゲート
* メインのジョブを開始したり、外部のデリゲートにバインドしたりするための `Activate()` 関数。
* バインドされた外部デレゲートを含む、クリーンアップのための `OnDestroy()` 関数。
* 外部のデリゲートにバインドされた場合のコールバック関数
* メンバー変数と内部ヘルパー関数

**注意：** `AbilityTasks` は1種類の出力デレゲートしか宣言できません。パラメータを使用するかどうかに関わらず、すべての出力デレゲートはこのタイプでなければなりません。未使用のデリゲートパラメータにはデフォルト値を渡します。

`AbilityTasks`は、所有する`GameplayAbility`を実行しているクライアントやサーバー上でのみ実行されます。しかし、`AbilityTask` のコンストラクタで `bSimulatedTask = true;` を設定し、`virtual void InitSimulatedTask(UGameplayTasksComponent& InGameplayTasksComponent);` をオーバーライドし、すべてのメンバ変数を複製するように設定することで、`AbilityTask` をシミュレートされたクライアント上で実行するように設定することができます。これは、ムーブメントの `AbilityTask` のように、すべての動きの変化を再現するのではなく、ムーブメントの `AbilityTask` 全体をシミュレートしたいような稀な状況でのみ有効です。すべての `RootMotionSource` `AbilityTasks` はこれを行います。例として、`AbilityTask_MoveToLocation.h/.cpp` を参照してください。

`AbilityTask` は、`AbilityTask` のコンストラクタで `bTickingTask = true;` を設定し、`virtual void TickTask(float DeltaTime);` をオーバーライドすると、`Tick` することができます。これは、フレーム間で値をスムーズに変換する必要がある場合に便利です。例として `AbilityTask_MoveToLocation.h/.cpp` を参照してください。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-using"></a>
### 4.7.3 アビリティタスクの使い方
C++で`AbilityTask`を作成して起動する方法（`GDGA_FireGun.cpp`より）。
```c++
UGDAT_PlayMontageAndWaitForEvent* Task = UGDAT_PlayMontageAndWaitForEvent::PlayMontageAndWaitForEvent(this, NAME_None, MontageToPlay, FGameplayTagContainer(), 1.0f, NAME_None, false, 1.0f);
Task->OnBlendOut.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnCompleted.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnInterrupted.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->OnCancelled.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->EventReceived.AddDynamic(this, &UGDGA_FireGun::EventReceived);
Task->ReadyForActivation();
```

ブループリントでは、`AbilityTask`用に作成したブループリントノードを使用するだけです。`ReadyForActivation()`を呼び出す必要はありません。これは `Engine/Source/Editor/GameplayTasksEditor/Private/K2Node_LatentGameplayTaskCall.cpp` によって自動的に呼び出されます。また、`K2Node_LatentGameplayTaskCall`は、`AbilityTask`クラスに`BeginSpawningActor()`と`FinishSpawningActor()`が存在する場合、自動的に呼び出します（`AbilityTask_WaitTargetData`参照）。繰り返しになりますが、`K2Node_LatentGameplayTaskCall`は、ブループリントのための自動化された魔術を行うだけです。C++ では、手動で `ReadyForActivation()`, `BeginSpawningActor()`, `FinishSpawningActor()` を呼び出さなければなりません。

![Blueprint WaitTargetData AbilityTask](https://github.com/dohi/GASDocumentation/raw/master/Images/abilitytask.png)

手動で `AbilityTask` をキャンセルするには、ブループリント (`Async Task Proxy` と呼ばれる) または C++ で `AbilityTask` オブジェクトに対して `EndTask()` を呼び出すだけです。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-rms"></a>
### 4.7.4 ルートモーションソースのアビリティータスク
GASには、`CharacterMovementComponent`にフックされた`RootMotionSource`を使って、ノックバック、複雑なジャンプ、プル、ダッシュなどのために`Characters`を時間をかけて動かすための`AbilityTasks`が付属しています。

**注意：** `RootMotionSource` の `AbilityTasks` の予測は、エンジンバージョン 4.19 および 4.25+ で動作します。エンジンバージョン4.20～4.24では予測がバグっています。しかし、`AbilityTasks`はマルチプレイヤーでは細かいネットの修正でその機能を果たし、シングルプレイヤーでは完璧に動作します。4.20-4.24のカスタムエンジンに4.25の[Prediction fix](https://github.com/EpicGames/UnrealEngine/commit/94107438dd9f490e7b743f8e13da46927051bf33#diff-65f6196f9f28f560f95bd578e07e290c)を組み込むことは可能です。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc"></a>
### 4.8 Gameplay Cues: ゲームプレイキュー

<a name="concepts-gc-definition"></a>
#### 4.8.1 ゲームプレイキューの定義
`GameplayCue` (`GC`) は、サウンドエフェクト、パーティクルエフェクト、カメラシェイクなど、ゲームに関連しないものを実行します。`GameplayCue`は通常、（ローカルで明示的に`Executed`、`Added`、`Removed`しない限り）複製され、予測されます。

`GameplayCue`のトリガーは、**`GameplayCue.`という必須の親名を持つ** 対応する`GameplayTag`とイベントタイプ（`Execute`, `Add`, `Remove`）を`ASC`を介して`GameplayCueManager`に送ることで行います。`GameplayCueNotify`オブジェクトや、`IGameplayCueInterface`を実装した他の`Actors`は、`GameplayCue`の`GameplayTag`(`GameplayCueTag`)に基づいて、これらのイベントを購読することができます。

**注意：** 繰り返しになりますが、`GameplayCue`の`GameplayTags`は`GameplayCue.`を親とする`GameplayTag`から始まる必要があります。例えば、有効な`GameplayCue`の`GameplayTag`は`GameplayCue.A.B.C`ということになります。

`GameplayCueNotifies`には、`Static`と`Actor`の2つのクラスがあります。これらは異なるイベントに反応し、異なるタイプの`GameplayEffects`がそれらをトリガーすることができます。対応するイベントをあなたのロジックでオーバーライドしてください。

| `GameplayCue` Class                                                                                                                  | Event             | `GameplayEffect` Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ------------------------------------------------------------------------------------------------------------------------------------ | ----------------- | ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`GameplayCueNotify_Static`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayCueNotify_Static/index.html) | `Execute`         | `Instant` or `Periodic`  | 静的な`GameplayCueNotifies`は、`ClassDefaultObject`（インスタンスを持たない）で動作し、ヒットの衝撃のような一回限りの効果に最適です。 |
| [`GameplayCueNotify_Actor`](https://docs.unrealengine.com/en-US/BlueprintAPI/GameplayCueNotify/index.html)                           | `Add` or `Remove` | `Duration` or `Infinite` | アクター `GameplayCueNotifies` は `Added` されると新しいインスタンスを生成します。これらはインスタンス化されているので、`Removed`されるまで時間をかけてアクションを行うことができます。これらはループするサウンドやパーティクルエフェクトに適しており、バッキング`Duration`や`Infinite`の`GameplayEffect`が削除されたときや、手動でremoveを呼び出したときに削除されます。これらのエフェクトには、同時に追加できる数を管理するオプションがあり、同じエフェクトを複数回使用しても、サウンドやパーティクルの開始は一度だけになります。 |

`GameplayCueNotifies`は技術的にはどのイベントにも対応できますが、一般的にはこのような使い方をします。

**注意：** `GameplayCueNotify_Actor` を使用する際には、`Auto Destroy on Remove` をチェックしてください。そうしないと、その後の `GameplayCueTag` の `Add` の呼び出しが機能しません。

`Full`以外の`ASC` [Replication Mode](#concepts-asc-rm)を使用している場合、`Add`と`Remove`の`GC`イベントはサーバープレイヤー(リッスンサーバー)に対して2回発生します。1回は`GE`を適用し、もう1回は `Minimal`の`NetMultiCast`からクライアントに送られます。ただし、`WhileActive`イベントはまだ1回しか発生しません。すべてのイベントは、クライアント上で一度だけ発生します。

サンプルプロジェクトには、スタンとスプリントのエフェクト用の`GamplayCueNotify_Actor`が含まれています。また、FireGunのプロジェクタイル・インパクト用の`GameplayCueNotify_Static`も用意されています。これらの`GC`は、`GE`で再現するのではなく、[ローカルにトリガーする](#concepts-gc-local)ことで、さらに最適化することができます。サンプルプロジェクトでは、初心者向けの使い方を紹介することにしました。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-trigger"></a>
#### 4.8.2 Gameplay Cueのトリガー

`GameplayEffect`が正常に適用されたとき(タグやイミュニティでブロックされていないとき)に、トリガーされるべきすべての`GameplayCue`の`GameplayTags`を記入します。

![GameplayEffectからトリガーされるGameplayCue](https://github.com/dohi/GASDocumentation/raw/master/Images/gcfromge.png)

`UGameplayAbility` はブループリントのノードに `Execute`, `Add`, `Remove` の `GameplayCue` を提供します。

![GameplayAbilityからトリガーされるGameplayCue](https://github.com/dohi/GASDocumentation/raw/master/Images/gcfromga.png)

C++では、`ASC`で直接関数を呼び出すことができます（または`ASC`のサブクラスでブループリントに公開することもできます）。

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
#### 4.8.3 ローカル・ゲームプレイ・キュー
`GameplayAbilities`と`ASC`から `GameplayCue`を発行するための関数は、デフォルトでは複製されています。各`GameplayCue`イベントは、マルチキャストのRPCです。これにより、多くのRPCが発生する可能性があります。また、GASではネットの更新ごとに同じ`GameplayCue`のRPCを2つまでしか使用できないようになっています。これを避けるために、可能な限りローカルの`GameplayCue`を使用します。ローカルの`GameplayCue`は、個々のクライアント上で`Execute`、`Add`、`Remove`のみを実行します。

ローカルな`GameplayCues`を使用することができるシナリオ。

* 射程距離の影響
* メレーの衝突の衝撃
* アニメーション・モンタージュから発せられる`GameplayCue`。

ローカルの `GameplayCue` 関数は、`ASC` サブクラスに追加する必要があります。

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

`GameplayCue` がローカルで `Added` された場合は、ローカルで `Removed` されなければなりません。レプリケーションで追加された場合は、レプリケーションで削除されます。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-parameters"></a>
#### 4.8.4 ゲームプレイキューのパラメータ
`GameplayCue` は、`GameplayCue` の追加情報を含む `FGameplayCueParameters` 構造体をパラメータとして受け取ります。`GameplayAbility` や `ASC` の関数から `GameplayCue` を手動でトリガーする場合には、`GameplayCue` に渡される `GameplayCueParameters` 構造体を手動で埋める必要があります。`GameplayEffect`によって`GameplayCue`がトリガーされる場合は、以下の変数が`GameplayCueParameters`構造体に自動的に入力されます。

* 集約されたソースタグ(AggregatedSourceTags)
* AggregatedTargetTags（集約されたターゲットタグ）
* GameplayEffectLevel
* AbilityLevel
* [EffectContext](#concepts-ge-context)
* マグニチュード (`GameplayEffect` が、`GameplayCue` タグコンテナの上のドロップダウンで選択されたマグニチュードの `Attribute` と、その `Attribute` に影響を与える対応する `Modifier` を持っている場合)

`GameplayCueParameters`構造体の`SourceObject`変数は、`GameplayCue`を手動でトリガーする際に、任意のデータを`GameplayCue`に渡すのに適した場所になる可能性があります。

**注意：** パラメータ構造の変数の中には、`Instigator`のように、`EffectContext`の中に既に存在しているものもあります。また、`EffectContext`には、ワールド内のどこに`GameplayCue`を配置するかを示す`FHitResult`を含めることができます。`EffectContext`をサブクラス化することで、より多くのデータを`GameplayCue`、特に`GameplayEffect`によってトリガーされる`GameplayCue`に渡すことが可能になります。

詳細については、[`UAbilitySystemGlobals`](#concepts-asg)にある、`GameplayCueParameters` 構造体を生成する3つの関数を参照してください。これらの関数は仮想的なものなので、オーバーライドしてより多くの情報を自動入力することができます。

```c++
/** Initialize GameplayCue Parameters */
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectSpecForRPC &Spec);
virtual void InitGameplayCueParameters_GESpec(FGameplayCueParameters& CueParameters, const FGameplayEffectSpec &Spec);
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectContextHandle& EffectContext);
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-manager"></a>
#### 4.8.5 Gameplay Cue Manager
デフォルトでは、`GameplayCueManager`はゲームディレクトリ全体をスキャンして`GameplayCueNotifies`を探し、プレイ時にメモリにロードします。`GameplayCueManager`がスキャンするパスは、`DefaultGame.ini`に設定することで変更できます。

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GameplayCueNotifyPaths="/Game/GASDocumentation/Characters"
```

`GameplayCueManager`には、すべての`GameplayCueNotifies`をスキャンして見つけてもらいたいのですが、プレイ時にすべての`GameplayCueNotify`を非同期にロードしてもらいたくはありません。これでは、すべての`GameplayCueNotify`とその参照されるサウンドやパーティクルが、レベルで使用されているかどうかに関わらず、メモリに格納されてしまいます。Paragonのような大規模なゲームでは、これが数百メガバイトもの不要なアセットをメモリ上に置くことになり、起動時のヒッチやゲームのフリーズの原因になります。

起動時にすべての`GameplayCue`を非同期にロードする代わりに、ゲーム内でトリガーされた`GameplayCue`だけを非同期にロードすることができます。これにより、すべての`GameplayCue`を非同期ロードする際の不要なメモリ使用やゲームのハードフリーズの可能性を軽減する代わりに、プレイ中に特定の`GameplayCue`が初めてトリガーされたときの効果が遅れる可能性があります。この潜在的な遅延は、SSDでは存在しません。HDDではテストしていません。UE Editorでこのオプションを使用する場合、Editorがパーティクルシステムをコンパイルする必要がある場合、GameplayCueの最初のロード中に若干の不具合やフリーズが発生することがあります。パーティクルシステムはすでにコンパイルされていますので、ビルド時には問題ありません。

まず、`UGameplayCueManager`をサブクラス化し、`DefaultGame.ini`で`AbilitySystemGlobals`クラスに`UGameplayCueManager`のサブクラスを使用するように指示します。

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GlobalGameplayCueManagerClass="/Script/ParagonAssets.PBGameplayCueManager"
```

`UGameplayCueManager`のサブクラスでは、`ShouldAsyncLoadRuntimeObjectLibraries()`をオーバーライドします。

```c++
virtual bool ShouldAsyncLoadRuntimeObjectLibraries() const override
{
	return false;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-prevention"></a>
#### 4.8.6 Gameplay Cuesの発火防止
時には、`GameplayCues`を発火させたくない場合があります。例えば、攻撃をブロックした場合、ダメージの`GameplayEffect`に付随するヒット・インパクトを再生せず、代わりにカスタムのものを再生したい場合があります。これは[`GameplayEffectExecutionCalculations`](#concepts-ge-ec)の中で、`OutExecutionOutput.MarkGameplayCuesHandledManually()`を呼び出して、`Target`や`Source'の`ASC`に`GameplayCue`イベントを手動で送信することで実現できます。

特定の`ASC`に対して`GameplayCues`を絶対に発生させたくない場合は、`AbilitySystemComponent->bSuppressGameplayCues = true;`と設定します。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-batching"></a>
#### 4.8.7 Gameplay Cue Batching
`GameplayCue`がトリガーされるたびに、unreliableなNetMulticast RPCが発生します。複数の`GC`を同時に起動する状況では、それらを1つのRPCに凝縮したり、データの送信量を減らして帯域幅を節約したりする最適化方法がいくつかあります。

<a name="concepts-gc-batching-manualrpc"></a>
##### 4.8.7.1 手動RPC
例えば、8つのペレットを発射するショットガンがあるとします。これは、8つのトレースとインパクトのある `GameplayCues` です。[GASShooter](https://github.com/dohi/GASShooter)では、すべてのトレース情報を[`EffectContext`](#concepts-ge-ec)の中に[`TargetData`](#concepts-targeting-data)として格納することで、これらを1つのRPCにまとめるという怠惰な方法をとっています。この方法ではRPCを8つから1つに減らすことができますが、1つのRPCでネットワーク上に大量のデータ（～500バイト）が送信されます。より最適化されたアプローチとしては、ヒットした場所を効率的にエンコードしたカスタム構造体でRPCを送信するか、受信側でインパクトのある場所を再現/近似するためにランダムなシード番号を与えることが考えられます。クライアントはこのカスタム構造体を解凍して、[ローカルに実行される`GameplayCues`](#concepts-gc-local)に戻します。

この仕組みは
1. `FScopedGameplayCueSendContext`を宣言します。これにより、`FScopedGameplayCueSendContext`がスコープから外れるまで、`UGameplayCueManager::FlushPendingCues()`が抑制され、すべての`GameplayCue`がキューイングされることになります。
1. `UGameplayCueManager::FlushPendingCues()`をオーバーライドして、カスタムの`GameplayTag`に基づいてバッチ処理が可能な`GameplayCue`をカスタム構造体にマージし、クライアントにRPCする。
1. クライアントはカスタム構造体を受け取り、それをローカルに実行される`GameplayCues`にアンパックします。

この方法は、`GameplayCueParameters`が提供しているパラメータではなく、`EffectContext`に追加したくないような特定のパラメータを`GameplayCues`に必要とする場合にも使用できます。例えば、ダメージ数、クリティカルインジケーター、ブレイクシールドインジケーター、フェイタルヒットインジケーターなどです。

https://forums.unrealengine.com/development-discussion/c-gameplay-programming/1711546-fscopedgameplaycuesendcontext-gameplaycuemanager

<a name="concepts-gc-batching-gcsonge"></a>
##### 4.8.7.2 1つのGEに複数のGCがある場合
`GameplayEffect`上のすべての`GameplayCue`は、すでに1つのRPCで送信されています。デフォルトでは、`UGameplayCueManager::InvokeGameplayCueAddedAndWhileActive_FromSpec()`は、`ASC`の`レプリケーション・モード`に関わらず、信頼性の低いNetMulticastで`GameplayEffectSpec`全体(ただし、`FGameplayEffectSpecForRPC`に変換されている)を送信します。これは、`GameplayEffectSpec`に何が含まれているかによって、多くの帯域幅を必要とする可能性があります。`AbilitySystem.AlwaysConvertGESpecToGCParams 1`を設定することで、これを最適化できる可能性があります。これにより、`GameplayEffectSpecs`が`FGameplayCueParameter`構造に変換され、`FGameplayEffectSpecForRPC`全体ではなく、それらをRPC化します。これにより、帯域幅を節約できる可能性がありますが、`GESpec` が `GameplayCueParameters` にどのように変換されるか、また、`GC` が何を知る必要があるかによって、情報が少なくなります。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-events"></a>
#### 4.8.8 ゲームプレイキューイベント
`GameplayCue` は特定の `EGameplayCueEvents` に反応します。

| `EGameplayCueEvent` | Description                                                                                                                                                                                                                                                                                                                         |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `OnActive`          | GameplayCue "が起動したときに呼び出される (追加) |
| `WhileActive`       | 実際には適用されていなくても（Join in progressなど）、`GameplayCue`がアクティブになると呼び出されます。これは `Tick` ではありません。`OnActive` と同様に、`GamplayCueNotify_Actor` が追加されたり、関連性が出てきたときに一度だけ呼び出されます。もし`Tick()`が必要ならば、`GameplayCueNotify_Actor`の`Tick()`を使えばいいのです。これは `AActor` なのだから。 |
| `Removed`           | `GameplayCue` が削除されたときに呼び出されます。このイベントに応答するブループリントの `GameplayCue` 関数は `OnRemove` です。 |
| `Executed`          | `GameplayCue` が実行されたときに呼び出されます: インスタントエフェクトまたは定期的な `Tick()` です。このイベントに応答するブループリントの `GameplayCue` 関数は `OnExecute` です。 |

`GameplayCue`の中で、`GameplayCue`の開始時に発生するが、遅れて参加した人が見逃しても構わないものには`OnActive`を使用します。`GameplayCue`の中で進行中のエフェクトで、遅れて参加した人に見てもらいたいものには`WhileActive`を使います。例えば、MOBAのタワー構造が爆発するという`GameplayCue`があった場合、最初の爆発パーティクルシステムと爆発音を`OnActive`に入れ、残りの進行中の火災パーティクルや音を`WhileActive`に入れます。このシナリオでは、遅れて参加した人が`OnActive`から最初の爆発を再生することは意味がありませんが、`WhileActive`から爆発が起こった後の地面上の持続的でループする火の効果を見てもらいたいと思います。`OnRemove`では、`OnActive`と`WhileActive`で追加されたものをクリーンアップします。`WhileActive` は、アクターが `GamplayCueNotify_Actor` の関連性の範囲に入るたびに呼び出されます。`OnRemove` はアクターが `GameplayCueNotify_Actor` の関連性のある範囲から離れる度に呼び出されます。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-reliability"></a>
#### 4.8.9 ゲームプレイキューの信頼性

一般的に`GameplayCue`は信頼性が低く、ゲームプレイに直接影響を与えるものには適していないと考えられます。

**実行される`GameplayCues`:** これらの`GameplayCues`は信頼性のないマルチキャストで適用され、常に信頼性がありません。

**`GameplayEffects`から適用される`GameplayCues`：**

* 自律的なプロキシが `OnActive`, `WhileActive`, `OnRemove` を確実に受信する。 
`FActiveGameplayEffectsContainer::NetDeltaSerialize()`は`UAbilitySystemComponent::HandleDeferredGameplayCues()`を呼び出し、`OnActive`と`WhileActive`を呼び出します。`FActiveGameplayEffectsContainer::RemoveActiveGameplayEffectGrantedTagsAndModifiers()`は`OnRemoved`の呼び出しを行います。
* シミュレートされたプロキシは、確実に `WhileActive` と `OnRemove` を受け取ります。 
`UAbilitySystemComponent::MinimalReplicationGameplayCues` のレプリケーションは `WhileActive` と `OnRemove` を呼び出す。OnActive`イベントは、信頼性のないマルチキャストによって呼び出されます。

**`GameplayEffect`を使用せずに`GameplayCues`を適用した場合：**

* 自律型プロキシが確実に `OnRemove` を受信する。 
信頼性のないマルチキャストによって、`OnActive`イベントと`WhileActive`イベントが呼び出されます。
* シミュレートされたプロキシは、信頼性の高い `WhileActive` と `OnRemove` を受信します。 
`UAbilitySystemComponent::MinimalReplicationGameplayCues` のレプリケーションは `WhileActive` と `OnRemove` を呼び出す。OnActive`イベントは、信頼性のないマルチキャストによって呼び出されます。

`GameplayCue`に「信頼性」が必要な場合は、`GameplayEffect`から適用し、FXの追加には`WhileActive`を、FXの削除には`OnRemove`を使用してください。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-asg"></a>
### 4.9 アビリティシステムグローバル
[`AbilitySystemGlobals`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemGlobals/index.html)クラスはGASのグローバルな情報を保持します。ほとんどの変数は`DefaultGame.ini`から設定できます。一般的にはこのクラスを使用する必要はありませんが、その存在を知っておくべきでしょう。[`GameplayCueManager`](#concepts-gc-manager)や[`GameplayEffectContext`](#concepts-ge-context)のようなものをサブクラス化する必要がある場合は、`AbilitySystemGlobals`を使って行います。

`AbilitySystemGlobals`をサブクラス化するには、`DefaultGame.ini`でクラス名を設定します。
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
AbilitySystemGlobalsClassName="/Script/ParagonAssets.PAAbilitySystemGlobals"
```

<a name="concepts-asg-initglobaldata"></a>
#### 4.9.1 InitGlobalData()
UE 4.24からは、[`TargetData`](#concepts-targeting-data)を使用するために、`UAbilitySystemGlobals::InitGlobalData()`を呼び出す必要があります。そうしないと、`ScriptStructCache`に関連するエラーが発生し、クライアントがサーバーから切断されます。この関数は、プロジェクト内で一度だけ呼び出す必要があります。FortniteではAssetManagerクラスのstart initial loading関数から、Paragonでは`UEngine::Init()`から呼び出されています。サンプルプロジェクトにあるように、`UEngineSubsystem::Initialize()`の中に入れるのが良いと思います。これは、`TargetData`の問題を回避するためにプロジェクトにコピーすべき定型的なコードと考えています。

`AbilitySystemGlobals`の`GlobalAttributeSetDefaultsTableNames`を使用中にクラッシュに遭遇した場合、Fortniteのように後から`UEngineSubsystem::Initialize()`ではなく、`AssetManager`や`GameInstance`で`UAbilitySystemGlobals::InitGlobalData()`を呼び出す必要があるかもしれません。このクラッシュは、`Subsystem`の作成順序が原因と考えられ、`GlobalAttributeDefaultsTables`では、`UAbilitySystemGlobals::InitGlobalData()`でデリゲートをバインドするために`EditorSubsystem`をロードする必要があります。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p"></a>
### 4.10 Prediction: 予測
GASはクライアントサイド予測をサポートしていますが、すべてを予測するわけではありません。GASのクライアントサイド予測とは、クライアントが `GameplayAbility` を起動して `GameplayEffect` を適用する際に、サーバーからの許可を待つ必要がないということです。クライアントは、サーバーが許可を与えることを「予測」し、`GameplayEffects` を適用する対象を予測することができます。サーバーは、クライアントが起動した後、ネットワークのレイテンシータイムで`GameplayAbility`を実行し、クライアントの予測が正しかったかどうかをクライアントに伝えます。クライアントが自分の予測のどれかに間違いがあった場合、クライアントは「予測違い」からの変更をサーバーに合わせて「ロールバック」します。

GAS関連の予測の決定的なソースは、プラグインのソースコードにある`GameplayPrediction.h`です。

Epicの考え方は、「逃げられる（訳注：ごまかしてそのままうまく通すことができる）」ものだけを予測するというものです。例えば、ParagonやFortniteはダメージを予測しません。ほとんどの場合、彼らはダメージに [`ExecutionCalculations`](#concepts-ge-ec) を使用していますが、これはいずれにせよ予測できません。これは、ダメージのような特定のものを予測しようとすることができないということではありません。もしあなたがそれをやってうまくいくなら、それは素晴らしいことです。

> 私たちは、「すべてをシームレスかつ自動的に予測する」というソリューションには賛成していません。私たちは、プレイヤーの予測は最小限にとどめるのがベストだと考えています（つまり、逃れられる最小限のものを予測する）。

*[Network Prediction Plugin](#concepts-p-npp) EpicのDave Ratti氏のコメントです*

**何が予測されるか：**
> * アビリティ起動
> * イベントのトリガ
> * GameplayEffectの適用
> * Attributeの変更（例外：現在、実行は予測せず、属性の変更のみを行う）
> * GameplayTagの修正
> * GameplayCueイベント（予測されたゲームプレイ効果の中からのものと、単独のものがある
> * モンタージュ
> * ムーブメント(UE4 UCharacterMovementに組み込まれています)

**予測されないもの：**
> * GameplayEffectの除去
> * GameplayEffectの周期的な効果(ドットのTick)

*`GameplayPrediction.h`* より

`GameplayEffect` の適用を予測することはできますが、`GameplayEffect` の除去を予測することはできません。この制限を回避する一つの方法は、`GameplayEffect` を除去したいときに、逆の効果を予測することです。例えば、移動速度が40%遅くなると予測します。予測して、40%の速度アップのバフを適用して除去することができます。そして、両方の`GameplayEffects`を同時に削除します。これは、すべてのシナリオに適しているわけではなく、 `GameplayEffects` の削除を予測するためのサポートが必要です。EpicのDave Ratti氏は、この機能を[将来のGASのイテレーション](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)に追加したいと表明しています。

`GameplayEffects` の削除を予測することができないため、`GameplayAbility` のクールダウンを完全に予測することができず、`GameplayEffect` の逆方向のワークアラウンドもありません。サーバーに複製された `Cooldown GE` はクライアント上に存在し、これを回避しようとする試み(例えば `Minimal` レプリケーション・モードなど)は、サーバーによって拒否されます。つまり、レイテンシーの高いクライアントは、サーバーにクールダウンを指示したり、サーバーの `Cooldown GE` が解除されたことを受け取るまでに時間がかかります。つまり、レイテンシーの高いプレイヤーは、レイテンシーの低いプレイヤーに比べて発射レートが低くなり、レイテンシーの低いプレイヤーに対して不利になります。Fortniteでは `Cooldown GE` の代わりにカスタムブックキーピングを使うことでこの問題を回避しています。

ダメージを予測することについては、GASを始めたときに多くの人が最初に試すことの一つであるにもかかわらず、個人的にはお勧めしません。特に、死を予測することはお勧めしません。ダメージを予測することはできますが、それはとても難しいことです。ダメージの予測を誤ると、敵のヘルスが跳ね上がってしまうのです。これは、死の予測をした場合、特に厄介で苛立たしいものになります。例えば、キャラクターの死を予測しなかった場合、そのキャラクターはラグドールを始めますが、サーバーが修正するとラグドールをやめてあなたに向かって撃ち続けます。

**注意：** `属性` を変化させる瞬間的な `GameplayEffects` （`コストGE` のようなもの）は、自分自身に対してはシームレスに予測することができますが、他のキャラクターに対する瞬間的な `属性` の変化を予測すると、その `属性` に短時間の異常や "blip（訳注：乱高下、急変）" が表示されます。予測された`Instant`の`GameplayEffects`は実際には`Infinite`の`GameplayEffects`と同じように扱われるので、予測が外れた場合はロールバックすることができます。サーバーの`GameplayEffect`が適用されると、同じ`GameplayEffect`が2つ存在する可能性があり、一瞬`Modifier`が2回適用されたり、全く適用されないことがあります。最終的には修正されますが、プレイヤーにはこの現象が目立つことがあります。

GASの予測実装が解決しようとしている問題点
> 1. "こんなことができるの？" 予測のための基本プロトコル。
> 2. "Undo" 予測が失敗したときに副作用を元に戻す方法。
> 3. やり直し（Redo） ローカルで予測した副作用がサーバーからも複製された場合、それを再生しないようにする方法。
> 4. "Completeness "全ての副作用を本当に予測したかどうかを確認する方法。
> 5. "Dependencies" 依存性のある予測と予測されたイベントの連鎖を管理する方法。
> 6. "オーバーライド" サーバーによって複製/所有されている状態を予測的にオーバーライドする方法。

*`GameplayPrediction.h` より*

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-key"></a>
#### 4.10.1 Prediction Key: 予測キー
GASの予測は、クライアントが`GameplayAbility`を有効にする際に生成する整数の識別子である`Prediction Key`の概念で動作します。

* クライアントは、`GameplayAbility`を起動するときに予測キーを生成します。これが `Activation Prediction Key` です。
* クライアントはこの予測キーを `CallServerTryActivateAbility()` でサーバに送信します。
* クライアントは、予測キーが有効な間に適用するすべての `GameplayEffects` に、この予測キーを追加します。
* クライアントの予測キーが範囲外になる。同じ`GameplayAbility`の中でさらに予測されるエフェクトは、新しい[Scoped Prediction Window](#concepts-p-windows)が必要になります。


* サーバーがクライアントから予測キーを受け取ります。
* サーバーは、適用するすべての`GameplayEffects`にこの予測キーを追加します。
* サーバーは予測キーをクライアントに複製します。


* クライアントはサーバーから複製された`GameplayEffects`を、それを適用するために使用された予測キーとともに受け取ります。複製された`GameplayEffects`の中に、クライアントが同じ予測キーで適用した`GameplayEffects`と一致するものがあれば、それらは正しく予測されたことになります。クライアントが予測したものを削除するまで、ターゲット上には一時的に2つの`GameplayEffect`のコピーが存在します。
* クライアントはサーバーから予測キーを受け取ります。これが `Replicated Prediction Key` です。この予測キーは現在、古くなったとマークされています。
* クライアントは、古い複製予測キーで作成した**すべての**`GameplayEffects`を削除します。サーバーで複製された`GameplayEffects`は持続します。クライアントが追加した`GameplayEffects`で、サーバーから一致するレプリケートされたバージョンを受け取らなかったものは、予測ミスです。

予測キーは、アクティベーション予測キーから`Activation`で始まる`GameplayAbilities`の命令「ウィンドウ」のアトミックなグループ化の間、有効であることが保証されます。これは1フレームの間だけ有効であると考えることができます。潜在的なアクションである`AbilityTask`からのコールバックは、`AbilityTask`が新しい[Scoped Prediction Window](#concepts-p-windows)を生成するビルトインのSynch Pointを持っていない限り、有効な予測キーを持たなくなります。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-windows"></a>
#### 4.10.2 アビリティの新しい予測ウィンドウの作成
`AbilityTasks`からのコールバックでより多くのアクションを予測するには、新しいScoped Prediction Keyで新しいScoped Prediction Windowを作成する必要があります。これは、クライアントとサーバー間のSynch Pointと呼ばれることもあります。入力関連のすべての `AbilityTasks` のような一部の `AbilityTasks` には、新しいScoped Prediction Windowを作成する機能が組み込まれており、`AbilityTasks` のコールバックに含まれるアトミックコードは、使用する有効なScoped Prediction Keyを持っていることになります。`WaitDelay`タスクのような他のタスクには、コールバックに新しいScoped Prediction Windowを作成するコードが組み込まれていません。そのような`AbilityTask` の後にアクションを予測する必要がある場合は、`WaitNetSync`  `AbilityTask` にオプション `OnlyServerWait` を指定して、手動で行う必要があります。クライアントが `WaitNetSync` で `OnlyServerWait` を指定してヒットすると、`GameplayAbility` の起動予測キーに基づいて新しいScoped Prediction Keyを生成し、それをサーバーに RPC して、適用する新しい `GameplayEffects` に追加します。サーバーは、`OnlyServerWait`で`WaitNetSync`にヒットすると、クライアントから新しいScoped Prediction Keyを受け取るまで待ってから続行します。このScoped Prediction Keyは、アクティベーション予測キーと同じように、`GameplayEffects`に適用され、クライアントにレプリケートされて古くなったとマークされます。Scoped Prediction Keyは、スコープから外れるまで、つまりScoped Prediction Windowが閉じてしまうまで有効です。この場合も、Scoped Prediction Keyを使用できるのは、アトミックな操作のみで、潜在的なものはありません。

Socped Prediction Windowは、必要な数だけ作成できます。

Synch Pointの機能を独自のカスタム`AbilityTask`に追加したい場合は、入力されたものがどのように`WaitNetSync` `AbilityTask`のコードを注入するかを見てください。

**注意：** `WaitNetSync` を使用すると、クライアントからの連絡があるまで、サーバーの `GameplayAbility` の実行が継続されないようにブロックされます。これは、ゲームをハックして、新しいScoped Prediction Keyの送信を意図的に遅らせる悪質なユーザーに悪用される可能性があります。Epicでは、`WaitNetSync` の使用を控えていますが、この問題が懸念される場合は、クライアントを介さずに自動的に実行が継続されるような、遅延時間のある`AbilityTask`の新バージョンを構築することをお勧めします。

サンプルプロジェクトでは、スプリントの `GameplayAbility` で `WaitNetSync` を使用して、スタミナコストを適用するたびに新しいScoped Prediction Windowを作成し、予測できるようにしています。理想的には、コストやクールダウンを適用する際に有効な予測キーが欲しいところです。

予測された`GameplayEffect`が所有するクライアントで2回再生されている場合、予測キーが古く、「やり直し」の問題が発生しています。通常は、`GameplayEffect`を適用する直前に`WaitNetSync`の`AbilityTask`に`OnlyServerWait`を付けて、新しいScoped Prediction Keyを作成することで解決できます。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-spawn"></a>
#### 4.10.3 予測的に産み出されるアクター
クライアント上で予測的に `Actor` を生成することは、高度なトピックです。GAS はこれを扱う機能を提供していません (`SpawnActor` `AbilityTask` は、サーバー上の `Actor` を生成するだけです)。重要なコンセプトは、クライアントとサーバーの両方で複製された `Actor` を生成することです。

もし、`Actor` が単なる化粧品であったり、ゲーム上の目的を果たさないのであれば、`Actor` の `IsNetRelevantFor()` 関数をオーバーライドして、サーバーが所有するクライアントへの複製を制限することで、簡単に解決できます。所有するクライアントはローカルにスポーンされたバージョンを持ち、サーバーや他のクライアントはサーバーの複製されたバージョンを持つことになります。
```c++
bool APAReplicatedActorExceptOwner::IsNetRelevantFor(const AActor * RealViewer, const AActor * ViewTarget, const FVector & SrcLocation) const
{
	return !IsOwnedBy(ViewTarget);
}
```

スポーンされた `Actor` が、ダメージを予測する必要のある投射物のようにゲームプレイに影響を与える場合は、このドキュメントの範囲外である高度なロジックが必要になります。Epic Games の GitHub で、UnrealTournament が予測して投射物をスポーンする方法を見てみましょう。ここでは、所有するクライアント上でのみ生成されるダミーの投射物を用意し、サーバーの複製された投射物と同期させています。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-future"></a>
#### 4.10.4 GASでの予測の将来性
`GameplayPrediction.h`では、将来的に `GameplayEffect` の除去や周期的な`GameplayEffect` を予測する機能を追加する可能性があるとしています。

EpicのDave Ratti氏は、クールダウンを予測する際に、レイテンシーが高いプレイヤーと低いプレイヤーでは不利になるという「レイテンシーリコンシリエーション」の問題を修正することに興味を示しています(https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)。

Epicによる新しい[Network Prediction plugin](#concepts-p-npp)は、以前のCharacterMovementComponentのように、GASと完全に相互運用できるようになることが期待されています。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-npp"></a>
#### 4.10.5 Network Prediction プラグイン
Epic は最近、 `CharacterMovementComponent`を新しい`Network Prediction`プラグインで置き換える取り組みを開始しました。このプラグインはまだ非常に初期の段階ですが、Unreal Engine の GitHub で非常に早い段階でアクセスすることができます。このプラグインが将来的にどのバージョンのエンジンで実験的なベータ版としてデビューするかは、現時点ではわかりません。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting"></a>
### 4.11 ターゲッティング

<a name="concepts-targeting-data"></a>
#### 4.11.1 ターゲットデータ
[`FGameplayAbilityTargetData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetData/index.html)は、ネットワークを介して渡されることを意図したターゲットデータのための一般的な構造です。`TargetData`は通常、`AActor`/`UObject`の参照、`FHitResults`、およびその他の一般的な位置/方向/オリジン情報を保持します。しかし、`GameplayAbilities`の[クライアントとサーバーの間でデータを受け渡す](#concepts-ga-data)ための簡単な手段として、サブクラス化して基本的に好きなものを入れることができます。基本構造体である`FGameplayAbilityTargetData`は直接使用するのではなく、サブクラス化することを意図しています。GASにはサブクラス化された`FGameplayAbilityTargetData`構造体がいくつか付属しており、`GameplayAbilityTargetTypes.h`に配置されています。

`TargetData`は通常、[`Target Actors`](#concepts-targeting-actors)によって生成されるか、**手動で作成され**、[`EffectContext`](#concepts-ge-context)を介して[`AbilityTasks`](##concepts-at)や[`GameplayEffects`](#concepts-ge)によって消費されます。`EffectContext`にあることで、[`Executions`](#concepts-ge-ec)、[`MMCs`](#concepts-ge-mmc)、[`GameplayCues`](#concepts-gc)、そして[`AttributeSet`](#concepts-as)のバックエンドにある関数は、`TargetData`にアクセスすることができます。

通常、`FGameplayAbilityTargetData`を直接渡すことはありませんが、代わりに、`FGameplayAbilityTargetData`へのポインタの内部TArrayを持つ[`FGameplayAbilityTargetDataHandle`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetDataHandle/index.html)を使用します。この中間構造体は、`TargetData`のポリモーフィズムをサポートします。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-actors"></a>
#### 4.11.2 ターゲットアクター
`GameplayAbility`では、ワールドからのターゲティング情報を視覚化して取得するために、`WaitTargetData`の`AbilityTask`で[`TargetActors`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor/index.html)を生成します。`TargetActors`はオプションで[`GameplayAbilityWorldReticles`](#concepts-targeting-reticles)を使用して現在のターゲットを表示することができます。確認後、ターゲット情報は[`TargetData`](#concepts-targeting-data)として返され、`GameplayEffects`に渡すことができます。

`TargetActor`は`AActor`をベースにしているので、StaticMeshやデカールなど、**どこ**や**どのように**ターゲットにしているかを表現するための、あらゆる種類の可視コンポーネントを持つことができます。StaticMeshは、キャラクターが作るオブジェクトの配置を視覚化するために使用できます。デカールは、地面上の効果範囲を示すのに使われます。サンプルプロジェクトでは、[`AGameplayAbilityTargetActor_GroundTrace`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor_Grou-/index.html)を使って、地面にデカールを貼り付けて、Meteorアビリティのダメージ効果範囲を表現しています。また、何かを表示する必要もありません。例えば、[GASShooter](https://github.com/dohi/GASShooter)で使われているような、ターゲットまでのラインを瞬時にトレースするヒットスキャンガンには、何も表示しなくても意味があります。

これらは基本的なトレースやコリジョンオーバーラップを使ってターゲット情報を取得し、その結果を `FHitResults` や `AActor` 配列として `TargetData` に変換します (`TargetActor` の実装に依存します)。`WaitTargetData` `AbilityTask` は、`TEnumAsByte<EGameplayTargetingConfirmation::Type> ConfirmationType` パラメータによって、ターゲットがいつ確認されるかを決定します。`TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant`を **使用しない** 場合、`TargetActor`は通常、`Tick()`でトレース/オーバーラップを実行し、その実装に応じて`FHitResult`にその位置を更新します。これは `Tick()` のトレース/オーバーラップを行いますが、レプリケートされておらず、通常は一度に複数の `TargetActor` を実行することはないので、一般的にはひどいものではありません (複数の可能性もありますが)。ただ、`Tick()`を使用していることと、GASShooterのロケットランチャーの副次的な能力のように、複雑な`TargetActor`が多くの処理を行う可能性があることに注意してください。`Tick()`でのトレースはクライアントへの反応が非常に良いのですが、パフォーマンスへの影響が大きすぎる場合は、`TargetActor`のティックレートを下げることを検討しても良いでしょう。`TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant` の場合、`TargetActor` は即座にスポーンし、`TargetData` を生成し、破棄します。Tick()は呼び出されません。

| `EGameplayTargetingConfirmation::Type` | When targets are confirmed                                                                                                                                                                                                                                                                                                                                     |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Instant`                              | 特別なロジックやユーザーの入力によって「発射」のタイミングを決めることなく、瞬時にターゲティングが行われます。 |
| `UserConfirmed`                        | ターゲティングは、[アビリティが `Confirm` 入力にバインドされている](#concepts-ga-input) または `UAbilitySystemComponent::TargetConfirm()` の呼び出しにより、ユーザーがターゲティングを確認したときに発生します。また、`TargetActor` はバインドされた `Cancel` 入力や `UAbilitySystemComponent::TargetCancel()` の呼び出しに反応してターゲティングをキャンセルします。 |
| `Custom`                               | GameplayTargeting Abilityは、`UGameplayAbility::ConfirmTaskByInstanceName()`を呼び出して、ターゲティングデータの準備ができたかどうかを判断する責任があります。また、`TargetActor`は`UGameplayAbility::CancelTaskByInstanceName()`に応答してターゲティングをキャンセルします。 |
| `CustomMulti`                          | GameplayTargeting Abilityは、`UGameplayAbility::ConfirmTaskByInstanceName()`を呼び出して、ターゲティングデータの準備ができたかどうかを判断する責任があります。また、`TargetActor`は`UGameplayAbility::CancelTaskByInstanceName()`に応答してターゲティングをキャンセルします。データ作成時に `AbilityTask` を終了してはいけません。 |

すべての`EGameplayTargetingConfirmation::Type`がすべての`TargetActor`でサポートされているわけではありません。例えば、`AGameplayAbilityTargetActor_GroundTrace` は `Instant` のConfirmをサポートしていません。

`WaitTargetData` `AbilityTask` はパラメータとして `AGameplayAbilityTargetActor` クラスを受け取り、`AbilityTask` の起動毎にインスタンスを生成し、`AbilityTask` の終了時に `TargetActor` を破棄します。`WaitTargetDataUsingActor` `AbilityTask` は既に生成された `TargetActor` を取り込みますが、`AbilityTask` の終了時には破棄されます。これらの `AbilityTask` はどちらも、使用するたびに `TargetActor` を生成するか、新たに生成する必要があるという点で非効率的です。プロトタイピングには適していますが、プロダクションでは、自動小銃のように常に `TargetData` を生成するようなケースがあれば、最適化を検討するとよいでしょう。GASShooter には、[`AgmemplayAbilityTargetActor`](https://github.com/dohi/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/GSGATA_Trace.h) のカスタムサブクラスと、`TargetActor` を破壊せずに再利用することができる、スクラッチで書かれた新しい [`WaitTargetDataWithReusableActor`](https://github.com/dohi/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/AbilityTasks/GSAT_WaitTargetDataUsingActor.h) `AbilityTask` が用意されています。

`TargetActor` はデフォルトでは複製されません。しかし、ローカルプレイヤーがどこをターゲットにしているかを他のプレイヤーに見せるために、ゲーム内で意味があれば複製させることができます。また、`WaitTargetData` `AbilityTask`のRPCを介してサーバーと通信する機能がデフォルトで含まれています。`TargetActor` の `ShouldProduceTargetDataOnServer` プロパティが `false` に設定されている場合、クライアントは `UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()` 内の `CallServerSetReplicatedTargetData()` を介して、確認時にその `TargetData` をサーバに RPC します。`ShouldProduceTargetDataOnServer`が`true`であれば、クライアントは`UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()`の中で、ジェネリックな確認イベントである`EAbilityGenericReplicatedEvent::GenericConfirm`のRPCをサーバーに送り、サーバーはRPCを受け取った時点でトレースやオーバーラップチェックを行い、サーバー上にデータを生成します。クライアントがターゲティングをキャンセルする場合は、`UAbilityTask_WaitTargetData::OnTargetDataCancelledCallback`で、ジェネリックなキャンセルイベントである`EAbilityGenericReplicatedEvent::GenericCancel`をサーバーにRPCで送ります。ご覧のとおり、`TargetActor` と `WaitTargetData` `AbilityTask` の両方にたくさんのデリゲートがあります。`TargetActor`は入力に応答して`TargetData`の準備完了、確認、キャンセルのデリゲートを生成してブロードキャストします。`WaitTargetData` は `TargetActor` の `TargetData` の準備完了、確認、キャンセルの各デリゲートを聞き、その情報を `GameplayAbility` とサーバーに返信します。サーバーに `TargetData` を送信する場合は、不正行為を防ぐために `TargetData` が妥当であることを確認するために、サーバー上で検証を行うとよいでしょう。サーバ上で`TargetData`を直接作成すると、この問題は完全に回避できますが、所有するクライアントの予測が狂う可能性があります。

使用する `AGamemplayAbilityTargetActor` の特定のサブクラスに応じて、異なる `ExposeOnSpawn` パラメータが `WaitTargetData` `AbilityTask` ノードで公開されます。一般的なパラメータには次のようなものがあります。

| Common `TargetActor` Parameters | Definition                                                                                                                                                                                                                                                                                                               |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Debug                           | `true`であれば、出荷されていないビルドにおいて、`TargetActor` がトレースを実行するたびに、デバッグトレース/オーバーラッピング情報を描画します。インスタントではない `TargetActor` は `Tick()` でトレースを行うので、これらのデバッグ描画の呼び出しも `Tick()` で行われることを覚えておいてください。 |
| Filter                          | [オプション] トレース/オーバーラップが発生したときに、ターゲットから `Actors` をフィルタリング (除去) するための特別な構造体です。典型的な使用例としては、プレイヤーの `Pawn` をフィルタリングしたり、ターゲットが特定のクラスであることを要求したりします。より高度な使用例については[ターゲットデータフィルタ](#concepts-target-data-filters)を参照してください。|
| Reticle Class                   | [オプション] `TargetActor` がスポーンする `AGamplayAbilityWorldReticle` のサブクラスです。 |
| Reticle Parameters              | [オプション] レティクルの設定を行います。レティクル](#concepts-targeting-reticles)を参照してください。 |
| Start Location                  | トレースを開始する場所を示す特別な構造体です。通常、これはプレイヤーの視点、武器の銃口、または `Pawn` の位置になります。| 

デフォルトの `TargetActor` クラスでは、`Actors` は、トレース/オーバーラップ内に直接いる場合のみ有効なターゲットとなります。彼らがトレース/オーバーラップから離れると (彼らが動くか、あなたが目をそらすと)、彼らはもはや有効ではありません。最後に有効だったターゲットを`TargetActor`に記憶させたい場合は、この機能をカスタムの`TargetActor`クラスに追加する必要があります。これらのターゲットは、`TargetActor` が確認やキャンセルを受け取るか、`TargetActor` がそのトレース/オーバーラップで新しい有効なターゲットを見つけるか、ターゲットが無効になる (破壊される) まで持続するので、持続的ターゲットと呼んでいます。GASShooterでは、ロケットランチャーのセカンダリアビリティのホーミングロケットのターゲットにパーシステントターゲットを使用しています。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-target-data-filters"></a>
#### 4.11.3 ターゲットデータフィルター
`Make GameplayTargetDataFilter`と`Make Filter Handle`の両ノードを使うと、プレイヤーの`Pawn`を除外したり、特定のクラスだけを選択することができます。より高度なフィルタリングが必要な場合は、`FGameplayTargetDataFilter`をサブクラス化して、`FilterPassesForActor`関数をオーバーライドします。
```c++
USTRUCT(BlueprintType)
struct GASDOCUMENTATION_API FGDNameTargetDataFilter : public FGameplayTargetDataFilter
{
	GENERATED_BODY()

	/** Returns true if the actor passes the filter and will be targeted */
	virtual bool FilterPassesForActor(const AActor* ActorToBeFiltered) const override;
};
```

ただし、これは `Wait Target Data` ノードに直接組み込むことはできません。なぜなら、`FGameplayTargetDataFilterHandle` が必要だからです。このサブクラスを受け入れるためには、新しいカスタムの`Make Filter Handle`を作成する必要があります。
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
#### 4.11.4 ゲームプレイアビリティ ワールドレティクルズ
[`AGamplayAbilityWorldReticles`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityWorldReticle/index.html) (`Reticle`) は、`インスタントではない`確定した [`TargetActors`](#concepts-targeting-actors)でターゲッティングする際に、誰をターゲッティングしているかを視覚化します。`TargetActors`は全ての`Reticle`のスポーンとデストロイのライフタイムに責任を持ちます。レティクル`は`AActors`なので、表現のためにあらゆる種類のビジュアルコンポーネントを使用することができます。[GASShooter](https://github.com/dohi/GASShooter)に見られるような一般的な実装は、スクリーンスペースにUMG Widgetを表示するために`WidgetComponent`を使用することです(常にプレイヤーのカメラを向いています)。レティクルは、どの `AActor` にいるのかわかりませんが、カスタム `TargetActor` でその機能をサブクラス化することができます。`TargetActor` は通常、`Tick()`ごとに `Reticle` の位置をターゲットの位置に更新します。

GASShooter では `Reticle` を使って、ロケットランチャーのセカンダリアビリティのホーミングロケットのロックオンターゲットを表示しています。敵の上にある赤いインジケータが `Reticle` です。同様の白い画像はロケットランチャーの十字線です。
![GASShooterのレティクル](https://github.com/dohi/GASDocumentation/raw/master/Images/gameplayabilityworldreticle.png)

レティクル」には、デザイナー向けの `BlueprintImplementableEvents` がいくつか付属しています (ブループリントで開発することを想定しています)。

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

`Reticle` はオプションとして、`TargetActor` が提供する [`FWorldReticleParameters`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FWorldReticleParameters/index.html) を設定に使用することができます。デフォルトの構造体では、1つの変数 `FVector AOEScale` しか提供していません。技術的にはこの構造体をサブクラス化することができますが、`TargetActor` は基本構造体しか受け入れません。デフォルトの `TargetActors` でこの構造体をサブクラス化できないのは、少し短絡的な気がします。しかし、独自のカスタム`TargetActor`を作った場合、独自のカスタム・レティクル・パラメータ構造体を提供し、それをスポーンする際に`AGameblayAbilityWorldReticles`のサブクラスに手動で渡すことができます。

`Recticles` はデフォルトでは複製されませんが、ローカルプレイヤーが誰をターゲットにしているかを他のプレイヤーに見せることがゲームにとって意味がある場合は、複製することができます。

`Reticles` は、デフォルトの`TargetActors`が設定された現在の有効なターゲットにのみ表示されます。例えば、`AGameplayAbilityTargetActor_SingleLineTrace`を使ってターゲットをトレースしている場合、`Reticle`は敵がトレースパスに直接入っている時にのみ表示されます。目をそらすと、その敵は有効なターゲットではなくなり、`Reticle`は消えてしまいます。`Reticle`を最後の有効なターゲットに留まらせたい場合は、最後の有効なターゲットを記憶し、そのターゲットに`Reticle`を留まらせるように`TargetActor`をカスタマイズする必要があります。これらは、`TargetActor` が確認やキャンセルを受け取るか、`TargetActor` がそのトレース/オーバーラップで新しい有効なターゲットを見つけるか、またはターゲットがもはや有効ではなくなる（破壊される）まで存続するので、私はこれらを永続的なターゲットと呼んでいます。 GASShooterでは、ロケットランチャーのセカンダリアビリティのホーミングロケットのターゲットにパーシステントターゲットを使用しています。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-containers"></a>
#### 4.11.5 Gameplay Effect Containers Targeting: ゲームプレイ効果コンテナのターゲティング
[`GameplayEffectContainers`](#concepts-ge-containers)には、[`TargetData`](#concepts-targeting-data)を生成するための、オプションで効率的な手段があります。このターゲティングは、`EffectContainer`がクライアントとサーバに適用されたときに即座に行われます。[`TargetActors`](#concepts-targeting-actors)よりも効率的です。これはターゲティングオブジェクトのCDO上で実行されるため(`Actors`のスポーンとデストロイがない)、プレイヤーからの入力がなく、確認を必要とせず即座に発生し、キャンセルできず、クライアントからサーバーにデータを送信できません(両方でデータを生成します)。インスタントトレースやコリジョンオーバーラップには効果的です。Epicの[Action RPG Sample Project](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)には、コンテナを使ったターゲティングの例として、能力の所有者をターゲットにしたり、イベントから`TargetData`を引き出したりする2つのタイプがあります。また、ブループリントにも実装されており、プレイヤーからあるオフセット (子ブループリント クラスによって設定される) でインスタント スフィア トレースを行うことができます。C++ やブループリントで `URPGTargetType` をサブクラス化して、独自のターゲティング タイプを作ることができます。

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
