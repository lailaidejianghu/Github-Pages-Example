---
layout:     post
title:    Unity - note
category: blog
description: 
---    
    //Clip.cs

    public class Clip{
        AudioClip clip;
        string name;
        public void Play(AudioSource source){
            source.clip=clip;
            source.Play();
        }
    }

---

    //Config.txt

    ClickButton ClickButton.mp3
    ClickButton1 ClickButton1.mp3
    ClickButton2 ClickButton2.mp3

---

    //ClipManager

    public class ClipManager{

        string[] clipName=new string[]();
        Clip[] allClips;
        public void ClipManager(){
            ReadConfig();
        }

        public void ReadConfig(){
            var fileAddress=System.IO.Path.Combine(Application.dataPaht,"/Config.txt");
            FileInfo fInfo=new FileInfo(fileAddress);
            if(fInfo.Exists){
                StreamReader r=new StreamReader(fileAddress);
             
               
               clipName= r.ReadToEnd().Split('/t');
               for (int i=0;i<clipName;i++){
                   string[] temp=clip[i].split('/t');
                    Clip tmpClip=new Clip(tmp[0],Resources.Load<AudioClip>(clipName(tmp[0])));
                
                    allClips[i]=tmpClip
               }
                
            }
            r.Close();
        }

       

        public Clip FindClipByName(string name){
            for(int i=0;i<allClips.Count;i++){
                if(allClips[i].name==name){
                    return allClips[i];
                }
            }
            return null;
        }
    }

---

    //AudioManager.cs


    public class AudioManager{
        void Start(){
            Instance=this;
            sourceManager=new SourceManager(gameObject);
            clipManager=new ClipManager();
        }
        public void Play(string audioName){
            AudioSource tmpSource=sourceManager.GetFreeAudio();
            Clip clip=clipManager.FindClipByName(audioName);
            if (clip!=null){
                clip.Play(tmeSource);
            }
        }

        public void Stop(string audioName){
            sourceNamager.Stop(audioName);
        }
    }

---

    // SourceManager.cs

    public class SourceManager{
        public void Stop(string audioName){
            for (int i=0;i<allSources.COunt;i++){
                if(allSources[i].isPlaying && allSources[i]clip.name.Equals(audioName)){
                    allSOurdces[i].Stop();
                }
            }
        }
    }
  

---
---
# TexturePacker 软件

属性设置

- Data Format:Unity - Texture2D sprite sheet

- Texture format: FNG(.png)

- Image format:RGBA8888

- Max size:W:2048,H:2048 (中高端，兼容所有机器：W:1024,H:1024)

- Other:default


# Unity - 插件：Texture importer


# MVC 

- M: data module

- V: view

- C: logical control

# UI - 搭建原则

- 以 panal 为单位

- 分层

UI管理模型：
                |        UIManager        |

              /                            \
            Panal1(UIBase.cs)          Panal2
            /    \                      /    \
        子控件1   子控件2(UIBehavious)  子控件1 子控件2
        

---

代码实现：

    //UIBase.cs
    public class UIBase:MonoBehaviour{
        
        void Awake(){
            Transform[]allChildren=transform.GetComponentsInChilerens<Transform>();

            for(int i=0;i<allChildren.Lenght;i++){
                if(allChildren[i].name.EndsWith("_N")){
                    allChildren[i].gameObject.AddComponent<UIBehavious>();
                }

                if(allChildren[i].name.EndsWith("_C")){
                    allChildren[i].gameObject.AddComponent<UISubManager>();
                }

                
            }
        }

        public GameObject GetWedgate(string widegateName){
            UIManager.Instance.getGameObject(transform.name,widegateName);
        }

        public UIBehavious GetUIBehaviour(string wedgeName){
            GameObject tmpObj=GetWedge(wedgeName);
            if(tmpObj!=null){
                return tmpObj.GetComponent<UIBEhaviour>();
            }
            return null;
        }

        public void AddButtonListen(string widegaName,UnityAction action){
            UIBehaviour be=getBehaviour(wedgeName);
            if(be){
                be.AddButtonListen(action);
            }
        }

        void OnDestroy(){
            UIManager.Instance.UnRegistGameObjectByPanal(transform.name);
        }

        public void AddDrag(string wedgeName,UnityAction<BaseEventData> action){
            UIBehaviour be=getBehaviour(wedgeName);
            if(be){
                be.AddDragInterface(action);
            }
        }

        public void AddSubButtonListen(string subManagerName,string wedgeName,Action action){
            UISubManager tmp=GetSubManager(subManagerName);
            if(tmp){
                tmp.AddButtonListen(wedgeName,action);
            }

        }

        public UISubManager getSubManager(string subManagerName){
            GameObject tmp= GetWedge(subManagerName);

            if(tmp){
                return tmp.GetComponent<UISubManager>();
            }

        }
        
        public Transform GetCanvas(){
            return UIManager.Instance.MainCanvas();
        }
    }

---

    //UIBehaviour.cs
    
    public clas UIBehaviour:MonoBehaviour{

        void Awake(){
            Transform parent=transform.getComponentInParent<UIBase>();
            UIManager.Instance.RegistGameObject(parent.name,transform.name,gameObject);
        }

        public void AddButtonListen(UnityAction action){
            Button btn=transform.GetComponent<Button>();
            if (btn){
                btn.onClick.AddListen(action)
            }
        }

        public void AddSliderListen(UnityAction<float> action){
            Slider slider=transform.GetComponent<Slider>();
            if (slider){
                slider.onValueChanged.AddListener(action);
            }
        }

        public void AddInputFiledValueListen(UnityAction<string> action){
            InputField iput=transform.GetComponent<InputField>();
            if (iput){
                iput.OnValueChanged.AddListener(action)
            }
        }

        public void AddInputFiledEndListen(UnityAction<string> action){
            InputField iput=transform.GetComponent<InputField>();
            if (iput){
                iput.OnEndEditd.AddListener(action)
            }
        }

        public void ChangeTextContent(string content){
            Text text=transform.GetComponent<Text>();
            if(text){
                text.text=content;
            }
        }

        public void AddDragInterface(UnityAction<BaseEventData> action) {
            EventTrigger trigger=gameObject.GetComponent<EventTrigger>();
            if(trigger){
                trigger=gameObject.AddComponent<EventTrigger>();           
            }
            EventTrigger.Entry entry=new EventTrigger.Entry();
            entry.eventID=EventTriggerType.Drag;
            entry.callback=new EventTrigger.TriggerEvent();
            entry.callbakc.AddListener(action);
            trigger.triggers.Add(entry);
        }
        
        //...other events
    }

---
    //UIManager.cs

    public class UIManager:MonoBehaviour{
        public static UIManager Instance;
        Dictionary<string,Dictionary<string,GameObject>> allWedge;
        void Awake(){
            Instance=this;
           allWedge=new Dictionary<string,Dictionary<string,GameObject>>();
        }

        public void RegistGameObject(string panaleName,string wedgeName,GameObject boj){
            if(!allWedge.ContainsKey(panleName)){
                allWedge[panleName]=new Dictionary<string,GameObject>();
            }
            allWedge[panleName][wedgeName].Add(wedgeName,GameObject);
        }

        public GameObject GetGameObject(string panelName,string wedgateName){
            if(allWedge.ContainsKey(panelName)){
                return allWedge[panalName][wedegateName];
            }
        }

        //remove child wedge
        public void UnRegistGameObject(string panaleName,string wedgeName){
            if(allWedge.ContainsKey(panleName) ){
                if(allWedge[panalName].ContainsKey(wedgeName)){
                     allWedge[panleName][wedgeName].Remove(wedgeName);
                }
              
            }     
        }

        publci void UnRegistGameObjectByPanal(
        string panaleName){
            if(allWedge.ContainsKey(panleName)  ){
                allWedge[panleName].Clear();
                allWedge[panleName]=null;
            }
              
            }     
        }

        private Transform mainCanvas;
        public Transform MainCanvas{
            get{
                return mainCanvas;
            }
            set{
                mainCanvas=GameObject.FindGameObjectWithTag("MainTag");
            }
        }

    }

---

    //UISubManager.cs 
    publc class UISubManager:Monobehaviour{
        Dictionary<string ,Transform> allChild;

        void Awake(){
            UIBase b=transform.GetComponentInParent<UIBase>();
            UIManager.Instance.RegistGameObject(b.name,transform.name,gameObject);
            allChild=new  Dictionary<string ,Transform>();
            Transform[] tmpChild=transform.getComponentsInChildren<Transform>();
            for(int i=0;i<tmpChild.Length;i++){
                tmpChild[i].name.EndsWith("_S")){
                    allChild.Add(tmpChild[i].name,tmpChild[i]);
                }
            }
        }

        public Transform GetChildTransform(string wedgeName){
            return allChild[widageName];
        }

        void OnDestroy(){
            if(allChild!=null){
                allChild.Clear();
                allChild=null;
            }
        }

        public void AddButtonListen(string wedgeName,Action action){
            Transform tmpTran=GetChildTransform(wedgeName);

            Button tmp=tmpTran.GetComponent<Button>();

            if(tmp){
                tmp.onClick.AddListener(action);
            }
        }
    }
逻辑流程：

- UIBehaviour 向UIManager注册。

- UIBase 将特定子控件遍历出来，并增加UIBehaviour控件.

- 将底层UIBehaviour的事件跟逻辑层绑定。

- UISubManager解决同一panal下同名子控件问题。


注意：
- 同一panal里，子控件需不一样
  
- 不同panal，名字需不一样


---
# Unity - 继承关系


                          Object Input GUI Physics Resources Time
                             |
    |——————————————|—————————|————————|—————|———————|——————|———————|————————|  
    |              |         |        |     |       |      |       |        |  
    GameObject Component AssetBundle Mesh Shader Material Avatar Motion Sprite
                   |                                               |
    |—————————————|————————|———————————|———————————|—————————|  AnimationClip   
    Transform Rigidbody Collider ParticleSystem Behavior Renderer
                           |                        |
                    CharacterControll               |
    |—————————|——————|————————|———————————|———————————|—————————|
    Camera Light Animation Animator AudioSource MonoBehavior NetworkView

