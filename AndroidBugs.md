

##Android Bugs List / Android Problems 

### UI 
#### 1.cannot perform this action after onsaveinstancestate 
    transaction.commit() -> transation.commitAllowingStateLoss();
众所周知，Android系统可以为了释放资源在任意时刻终止进程，有些后台运行的进程可能都不会有任何提示的被杀。而onSaveInstanceState()就是给activity一个最后机会去保存它需要保存的东西，bundle对象就是所要保存信息的载体被传到系统服务进程，如果有朝一日该activity被重新创建时，该对象就会被传回帮助activity重现先前的状态。那么抛出异常的原因在哪儿呢？事实上bundle对象仅仅是activity在调用onSaveInstanceState()时的快照，也就意味着在onSaveInstanceState()后使用FragmentTransaction#commit()时，bundle并没有对该transaction作保存而在activity还未恢复到最初状态时调用，这样便会出现UI的丢失，为了保护用户体验IllegalStateException就被抛出了。
版本差异


1 在activity生命周期方法内使用FragmentTransaction#commit()应谨慎
   多数的应用会在onCreate()中使用该方法，所以不会遇到这样的问题，但如果在onActivityResult(), onStart()或onResume()中使用时就有可能遇到了。那么如果因为一些原因一定要在这些方法中使用FragmentTransaction#commit()该怎么办呢（比如说从下一个页面回来时需要刷新界面的时候）。这时最好在FragmentActivity#onResumeFragments()或者Activity#onPostResume()中使用。这两个方法会保证在activity恢复到最初状态后被调用。

2 commitAllowingStateLoss()只能当做最后的手段
commit和commitAllowingStateLoss()之间的唯一区别是如果状态丢失后者仅仅是不抛出异常而已。一般情况下你不会想用这个方法因为它仍然会有状态丢失的可能。









### Gradle
