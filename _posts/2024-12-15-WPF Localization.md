---
title: WPF Localization
tags:
  - C#
  - WPF
  - Localization
---

## Localization 이란
<!--more-->
 프로그램을 개발 중 지역화(Localization)문제가 발생 할 때가 있다. 간단하게 프로그램을 서비스해야하는 지역이 해외인 경우 해당 국가에 맞는 언어로 작업을 해야한다.
 간혹 고객사 요구사항 중에 런타임에 언어를 바꿀수 있는 기능을 요구하는 경우가 있다.

## WPF Localization 방법
 WPF프로젝트를 생성하면 Properties에 'Resouces.resx'라는 파일이 있다.
 이 파일을 복사하여 언어별로 나눈다.
 영문, 한글이면  다음과 같이 3개의 파일을 만든다(복사하여).
   
  - Resource.resx
  - Resource.ko.resx
  - Resource.en.resx  
  
  이후 아이디와 문자열을 입력하여 테이블을 작성한다. 
  본인은 세개의 파일에 모두 동일한 아이디를 작성하고 문자열만 다르게 작업하였다.  
  
  다음 클래스로 파일을 하나 만든다.

 {% highlight c# %}
  {% raw %}
namespace MVVMBase
{
    public class LocExtension : Binding
    {
        public LocExtension(string name) : base("[" + name + "]")
        {
            this.Mode = BindingMode.OneWay;
            this.Source = TranslationSource.Instance;
        }
    }

    class TranslationSource : INotifyPropertyChanged
    {
        static public CultureInfo LANG_EN = new CultureInfo("en-us");
        static public CultureInfo LANG_KO = new CultureInfo("ko-kr");
        private const char KeySeparator = '_';
        private static readonly TranslationSource instance = new TranslationSource();

        public static TranslationSource Instance
        {
            get { return instance; }
        }
        Resources.ResourceManager;
        private readonly ResourceManager resManager;

        public TranslationSource()
        {
            Assembly assembly = Assembly.GetExecutingAssembly();
            string strBaseName = assembly.GetName().Name + ".Properties.Resources";
            resManager = new ResourceManager(strBaseName, assembly);
        }

        private CultureInfo currentCulture = null;

        public string this[string key]
        {

            get
            {
                if (key.IndexOf(KeySeparator) == -1)
                {
                    var res = this.resManager.GetString(key, this.currentCulture);
                    return !string.IsNullOrWhiteSpace(res) ? res : $"[{key}]";
                }
                else
                {
                    var keys = key.Split(KeySeparator);
                    var results = new List<string>();
                    foreach (var k in keys)
                    {
                        var res = this.resManager.GetString(k, this.currentCulture);
                        results.Add(!string.IsNullOrWhiteSpace(res) ? res : $"[{k}]");
                    }
                    return string.Join(" ", results);
                }
            }
        }

        public CultureInfo CurrentCulture
        {
            get { return this.currentCulture; }
            set
            {
                if (this.currentCulture != value)
                {
                    this.currentCulture = value;
                    var @event = this.PropertyChanged;
                    if (@event != null)
                    {
                        @event.Invoke(this, new PropertyChangedEventArgs(string.Empty));
                    }
                }
            }
        }

        public event PropertyChangedEventHandler PropertyChanged;
    }
}
  {% endraw %}
{% endhighlight %}

 언어를 바꾸거나 추가해야할 때 CultureInfo를 각 국가에 맞게 검색하여 추가한다.
 
 문자열을 대치하는 방법은 코드에서는 다음과 같이 사용한다.  
 id는 위에서 작성한 테이블에 원하는 문자열 아이디이다.

 {% highlight c# %}
  {% raw %}
  string strLoc = TranslationSource.Instance["id"]
   {% endraw %}
{% endhighlight %}

 xaml에서는 다음과 같이 사용한다.

{% highlight xml %}
  {% raw %}
  <Window x:Class="Roll2Roll.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:mvvm="clr-namespace:MVVMBase"
        mc:Ignorable="d"
        Title="MainWindow" WindowStyle="None" 
        >
    <Button Click="bn_Click"Content="{mvvm:Loc id}"/>
  </Window>
  {% endraw %}
{% endhighlight %}

  
## 언어를 교체하는 방법
   이제 사용법과 준비를 마치고, 런타임에 교체하는 코드를 소개한다.
   아래 코드를 버튼 이벤트나 원하는 이벤트에 작성을 하면, 해당 이벤트 발생 시 언어가 바뀐다.

{% highlight xml %}
  {% raw %}
   if (TranslationSource.Instance.CurrentCulture == TranslationSource.LANG_EN)
        TranslationSource.Instance.CurrentCulture = TranslationSource.LANG_KO;
    else
        TranslationSource.Instance.CurrentCulture = TranslationSource.LANG_EN;
  {% endraw %}
{% endhighlight %}