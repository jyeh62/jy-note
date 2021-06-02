---
type: ~/Documents/0_개인/개인메모/docs/android
keywords: hello world
created : 2021/05/28/10:59
---

# View Binding

- 최대한 Android/Device 관련 객체나 메소드는 사용하지 않도록 설계 필요
- 가능하다면 Fragment 에 한개의 ViewModel을 사용, 그렇지만 너무 길어지지 않도록 적절히 나눠서 사용
- LiveData를 사용 → lifecycle
- RX를 사용할 경우, lifecycle 관리 필요함

## 생성 방법

1. 파라미터가 없는 ViewModel - ViewModelProvider.NewInstanceFactory
    1. noParamViewModel = ViewModelProvider(ViewModelStoreOwner, ViewModelProvider.NewInstanceFactory()).get(NoParamViewModel::class.java)

2. 파라미터가 없는 ViewModel - Lifecycle Extensions
    1. noParamViewModel = ViewModelProvider(ViewModelStoreOwner).get(NoParamViewModel::class.java)

3. ViewModelProvider.Factory를 사용
    1. custom view model provider factory 구현

        ```kotlin
        class HasParamViewModelFactory(private val param: String) : ViewModelProvider.Factory {
            override fun <T : ViewModel?> create(modelClass: Class<T>): T {
                return if (modelClass.isAssignableFrom(HasParamViewModel::class.java)) {
                    HasParamViewModel(param) as T
                } else {
                    throw IllegalArgumentException()
                }
            }
        }
        ```

        ```kotlin
        @Suppress("UNCHECKED_CAST")
        class ViewModelFactory constructor(
                private val tasksRepository: TasksRepository,
                        private val handle : Handle
        ) : ViewModelProvider.Factor {

            override fun <T : ViewModel> create(
                    modelClass: Class<T>
            ) = with(modelClass) {
                when {
                    isAssignableFrom(StatisticsViewModel::class.java) ->
                        StatisticsViewModel(tasksRepository)
                    isAssignableFrom(TaskDetailViewModel::class.java) ->
                        TaskDetailViewModel(tasksRepository)
                    isAssignableFrom(AddEditTaskViewModel::class.java) ->
                        AddEditTaskViewModel(tasksRepository)
                    isAssignableFrom(TasksViewModel::class.java) ->
                        TasksViewModel(tasksRepository, handle)
                    else ->
                        throw IllegalArgumentException("Unknown ViewModel class: ${modelClass.name}")
                }
            } as T
        }
        ```

   2. View Model 생성

        ```kotlin
        hasParamViewModel = ViewModelProvider(this, HasParamViewModelFactory(sampleParam)).get(HasParamViewModel::class.java)
        ```

4. AndroidViewModel
    Context 등 Android 기능을 사용하고자 할 때  AndroidViewModel를 생성하는 경우 생성 방법이 다르다

   1. Parameter가 없는 경우 AndroidViewModelFactory를 사용

        ``` kotlin
        noParamAndroidViewModel = ViewModelProvider(this, ViewModelProvider.AndroidViewModelFactory(application)).get(NoParamAndroidViewModel::class.java)
        ```

    2. Parameter가 있는 경우는 custom factory를 만들어 사용한다.

        ```kotlin
        class HasParamAndroidViewModel(application: Application, val param: String): AndroidViewModel(application)
        ```

        ```kotlin
        class HasParamAndroidViewModelFactory(private val application: Application, private val param: String)
            : ViewModelProvider.NewInstanceFactory() {
        
            override fun <T : ViewModel?> create(modelClass: Class<T>): T {
                if (AndroidViewModel::class.java.isAssignableFrom(modelClass)) {
                    try {
                        return modelClass.getConstructor(Application::class.java, String::class.java)
                            .newInstance(application, param)
                    } catch (e: NoSuchMethodException) {
                        throw RuntimeException("Cannot create an instance of $modelClass", e)
                    } catch (e: IllegalAccessException) {
                        throw RuntimeException("Cannot create an instance of $modelClass", e)
                    } catch (e: InstantiationException) {
                        throw RuntimeException("Cannot create an instance of $modelClass", e)
                    } catch (e: InvocationTargetException) {
                        throw RuntimeException("Cannot create an instance of $modelClass", e)
                    }
                }
                return super.create(modelClass)
            }
        }
        ```