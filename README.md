# 3D-UI
#  UI 效果制作
## Quest Log 公告牌
![这里写图片描述](https://img-blog.csdn.net/20180603221814195?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d1eXk3NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  
### 主要思路与细节
> 做得还比较简单，细节还要修改  
> 主要采用协程和mono.StartCoroutine以及callback()实现动画效果，同时也使得代码简洁。  
> 关键代码如下  


```
//Start()里面添加点击事件
Button btn=this.GetComponent<Button>();
btn.onClick.AddListener (OnClick);
```
> mono.StartCoroutine 部分  
> 因为用了callback()所以调用fold时候的StartCoroutine 的参数留意一下  

```
void OnClick() {
	//Debug.Log ("OnClick");
	if (!isFold) {
		mono.StartCoroutine (fold (0.3f, () => {
			MonoBehaviour.print ("end of folding it!");
		}));
		isFold = true;
	} else {
		mono.StartCoroutine (unfold (0.3f, () => {
			MonoBehaviour.print ("end of unfolding it!");
		}));
		isFold = false;
	}
}
```
> 协程部分  
> 协程部分的yield return null;不要忘记了  
> 为了持续时间加上了callback，这个看个人需求添加  
> for (float f = time; f >= 0.0f; f -= Time.deltaTime)的实际作用可以等于Update()  
```
IEnumerator unfold(float time,Action callback){
	//Debug.Log ("unfold");
	float width=text1.GetComponent<RectTransform> ().sizeDelta.x;
	float HEIGHT=130;
	float speedheigth = HEIGHT / time*Time.deltaTime;
	float height = 0;

	for (float f = time; f >= 0.0f; f -= Time.deltaTime) {
		if (text2.GetComponent<RectTransform> ().sizeDelta.y <HEIGHT) {
			height += speedheigth;
			text2.GetComponent<RectTransform> ().sizeDelta = new Vector2 (width,height);
			yield return null;  
		} else {
			text2.GetComponent<RectTransform> ().sizeDelta = new Vector2 (width,HEIGHT);
			yield return null;  
		}
	}
	callback ();
}
```

### 留意的细节和图形窗口设置
> 整体的图形层  
![这里写图片描述](https://img-blog.csdn.net/20180603223412788?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d1eXk3NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  
> 将rect transform部分pivot等提前统一设置  
![这里写图片描述](https://img-blog.csdn.net/20180603223634617?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d1eXk3NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  
> 调整滑动框以及显示大小，调整Viewport下面的Content即可  
![这里写图片描述](https://img-blog.csdn.net/20180603223919168?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d1eXk3NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  
  
### 源代码  

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using System;

public class Button2 : MonoBehaviour {
	GameObject text1,text2;

	MonoBehaviour mono; 
	bool isFold=true;
	// Use this for initialization
	void Start () {
		//添加点击事件
		Button btn=this.GetComponent<Button>();
		btn.onClick.AddListener (OnClick);
		text2 = GameObject.FindWithTag ("Newtext2");
		text1=GameObject.FindWithTag ("Newtext1");
		mono= transform.GetComponents<MonoBehaviour> () [0];

	}
	// Update is called once per frame
	void Update () {
		this.transform.position = new Vector2 (this.transform.position.x,
			(text1.transform.position.y - text1.GetComponent<RectTransform> ().sizeDelta.y-10));
	}
	void OnClick() {
		//Debug.Log ("OnClick");
		if (!isFold) {
			mono.StartCoroutine (fold (0.3f, () => {
				MonoBehaviour.print ("end of folding it!");
			}));
			isFold = true;
		} else {
			mono.StartCoroutine (unfold (0.3f, () => {
				MonoBehaviour.print ("end of unfolding it!");
			}));
			isFold = false;
		}
	}

	IEnumerator fold(float time,Action callback){
		//Debug.Log ("fold text1.GetComponent<RectTransform> ().sizeDelta.y: "+text1.GetComponent<RectTransform> ().sizeDelta.y);
		float width=text2.GetComponent<RectTransform> ().sizeDelta.x;
		float heigth=text2.GetComponent<RectTransform> ().sizeDelta.y;
		float speedheigth = heigth / time*Time.deltaTime;

		for (float f = time; f >= 0.0f; f -= Time.deltaTime) {
			if (text2.GetComponent<RectTransform> ().sizeDelta.y > 0) {
				heigth -= speedheigth;
				text2.GetComponent<RectTransform> ().sizeDelta = new Vector2 (width, heigth);
				yield return null;  
			} else {
				text2.GetComponent<RectTransform> ().sizeDelta = new Vector2 (width, 0);
				yield return null;  
			}
		}
		callback ();
	}
	IEnumerator unfold(float time,Action callback){
		//Debug.Log ("unfold");
		float width=text1.GetComponent<RectTransform> ().sizeDelta.x;
		float HEIGHT=130;
		float speedheigth = HEIGHT / time*Time.deltaTime;
		float height = 0;

		for (float f = time; f >= 0.0f; f -= Time.deltaTime) {
			if (text2.GetComponent<RectTransform> ().sizeDelta.y <HEIGHT) {
				height += speedheigth;
				text2.GetComponent<RectTransform> ().sizeDelta = new Vector2 (width,height);
				yield return null;  
			} else {
				text2.GetComponent<RectTransform> ().sizeDelta = new Vector2 (width,HEIGHT);
				yield return null;  
			}
		}
		callback ();
	}
}

```
