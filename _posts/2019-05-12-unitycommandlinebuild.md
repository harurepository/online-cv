---
title: "Unity Monobehaviour Lifecycle"
date: 2019-05-12 20:49:00 +0900
categori: Unity
---
Unity는 기본적으로 Command Line으로 Editor에 있는 클래스에 접근하여 명령을 내릴수 있는데 이를 이용하여 Gradle에 적용하여 빌드 할예정이다.
필요 지식 : Unity Custom Editor, Unity Script Build
Command Line으로 Unity에 명령을 호출하기 위해서는 Unity에서 Custom Editor 클래스를 이용하여 먼저 타겟이 되는 플랫폼의 Build를 진행하여야 한다.
Unity의 예약된 폴더명중 하나인 Editor는 Unity Editor 내에서만 동작하는 클래스로 유니티 내에서만 사용이 가능하고 실제 Release된 곳에서는 영향을 주지 않는다. 따라서 이 폴더안에 빌드할 스크립트가 담긴 클래스를 만들어 넣어야 한다.
상위 폴더가 어디이건 상관없이 Editor 폴더 아래에 두면 Editor 클래스가 되니 Editor 폴더를 만들어 안에다 빌드 클래스를 넣도록 하자
난 Editor/BuildEditor.cs 로 만들어 넣었다.
커맨드 라인에서 접근하기 쉽게 하기 위해서는 static 메서드 형식으로 실행할 매서드를 만드는것이 좋다.
예제는 아래와 같다.

---
```csharp
using UnityEngine;
using UnityEditor;
using System.Collections;
using System.Collections.Generic;

public class BuildEditor
{    
	[MenuItem("Build/Android")]
	public static void BuildAndroid()
	{
		string buildPath = absPath + "/Android.apk";
		BuildPipeline.BuildPlayer( BuildEditor.GetScenes(), buildPath, BuildTarget.Android, BuildOptions.None );
	}    
	
	[MenuItem("Build/iOS")]
	public static void BuildiOS()
	{
		string buildPath = absPath + "/Xcode";BuildPipeline.BuildPlayer( BuildEditor.GetScenes(), buildPath, BuildTarget.iOS, BuildOptions.AcceptExternalModificationsToPlayer );
	}
	
	public static string absPath
	{
		get
		{
#if UNITY_EDITOR
			string absPath = Application.dataPath;
			return absPath.Substring(0, absPath.LastIndexOf("/"));
#endif
			return Application.dataPath;
		}
	}
	
	public static string[] GetScenes()
	{
		var scenes = EditorBuildSettings.scenes;
		var sceneList = new List<string>();
		foreach (var scene in scenes)
		{
			if (scene.enabled)
			{
				sceneList.Add(scene.path);
			}
		}
		return sceneList.ToArray();
	}
}
```
---


이제  BuildEditor.BuildAndroid, BuildEditor.BuildiOS를 통해 메서드를 접근가능하게 되었다.
Custom Editor용 클래스를 하나 생성하여 클래스에 빌드내용을 작성하여
Unity의 Command Line 명령에 대해 자세하게 알고 싶다면 아래 링크로 접속하면 된다.

http://docs.unity3d.com/Manual/CommandLineArguments.html
