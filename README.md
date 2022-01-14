# Sunflower_네이게이션
## [네비게이션](https://developer.android.com/guide/navigation?hl=ko)
앞선 [Sunflower_info](https://github.com/tnvnfdla1214/Sunflower_info) 포스팅에서 말했듯이 이 프로젝트는 SPA(Single-Page-Application) 싱글 액티비티 디자인 구조로 되어있으므로 하나의 액티비티와 다수의 프래그먼트가 존재합니다.

이 프로젝트에서는 Jetpack Navigation에서 제공해주는 바텀네비게이션, 툴바를 사용하지는 않았고 메인 액티비티에 Toolbar + ViewPager2 + TabLayout 구조를 가진 Fragment 가 세팅되고 프래그먼트가 전환되는 식입니다. 추가로 프래그먼트에서 아이템을 누르면 상세화면으로 가는 네비게이션을 갖고있습니다.

Jetpack Navigation은 싱글 액티비티 디자인, 프래그먼트를 적극적으로 활용할때 강한 이점이 있습니다.

## 디자인
### GardenActivity XML(activity_garden.xml)
```Kotlin
<layout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/nav_host"
        android:name="androidx.navigation.fragment.NavHostFragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:defaultNavHost="true"
        app:navGraph="@navigation/nav_garden" />

</layout>
```
단 하나인 메인 액티비티를 담당하는 부분이다. XML을 보면 navGraph와 defaultNavHost가 설정되어 있습니다.
- android:name="androidx.navigation.fragment.NavHostFragment"
  - 호스트 역활(메인 역활)
  - 메인 액티비티는 Content와 Navigation Method는 모두 NavHost 라는 Fragment에게 위임합니다.
  - 메인 액티비티의 xml 은 싱글 액티비티 구성일 경우 Global 한 네비게이션을 가지는 역할을 합니다.
- app:navGraph="@navigation/nav_garden"
  - NavHostFragment 와 설정한 네비게이션 그래프를 연결(연관)시켜줍니다. 네비게이션 그래프는 NavHostFragment에서 사용자가 탐색 할 수있는 모든 목적지를 지정합니다
- app:defaultNavHost="true"
  - 뒤로가기 버튼 클릭시 NavHostFragment가 시스템 뒤로 버튼을 가로채 돌아옵니다.(안할 경우 뒤로가기 버튼 클릭시 호스트화면으로 안가고 바로 앱이 종료될 것 입니다.)
  - 하나의 NavHost만 기본값으로 지정할 수 있습니다.
  - 동일한 레이아웃에 여러 호스트가 있다면(예: 창이 2개인 레이아웃) 한 호스트만 기본 NavHost로 지정해야 합니다.
### NavHostFragment(nav_garden.xml)
 ```Kotlin
 //nav_garden.xml
<navigation xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    app:startDestination="@id/view_pager_fragment">

    <fragment
        android:id="@+id/view_pager_fragment"
        android:name="com.google.samples.apps.sunflower.HomeViewPagerFragment"
        tools:layout="@layout/fragment_view_pager">

        <action
                android:id="@+id/action_view_pager_fragment_to_plant_detail_fragment"
                app:destination="@id/plant_detail_fragment"
                app:enterAnim="@anim/slide_in_right"
                app:exitAnim="@anim/slide_out_left"
                app:popEnterAnim="@anim/slide_in_left"
                app:popExitAnim="@anim/slide_out_right" />
    </fragment>

    <fragment
        android:id="@+id/plant_detail_fragment"
        android:name="com.google.samples.apps.sunflower.PlantDetailFragment"
        android:label="@string/plant_details_title"
        tools:layout="@layout/fragment_plant_detail">

        <action
            android:id="@+id/action_plant_detail_fragment_to_gallery_fragment"
            app:destination="@id/gallery_fragment"
            app:enterAnim="@anim/slide_in_right"
            app:exitAnim="@anim/slide_out_left"
            app:popEnterAnim="@anim/slide_in_left"
            app:popExitAnim="@anim/slide_out_right" />
        <argument
            android:name="plantId"
            app:argType="string" />
    </fragment>

    <fragment
        android:id="@+id/gallery_fragment"
        android:name="com.google.samples.apps.sunflower.GalleryFragment"
        android:label="@string/plant_details_title"
        tools:layout="@layout/fragment_gallery">
        <argument
            android:name="plantName"
            app:argType="string" />
    </fragment>

</navigation>
```
- app:startDestination="@id/view_pager_fragment"
  - FragmentContainerView 에 ViewPager2인 HomeViewPagerFragment가 연결 됩니다.
### Arguments
![image](https://user-images.githubusercontent.com/48902047/149464156-ff0b9fe9-a755-4e2a-945c-e320a4b91c53.png)
![image](https://user-images.githubusercontent.com/48902047/149464448-9b241ea9-d6d0-4566-90bc-0a269de76bba.png)

Jetpack Navigation의 디자인은 위와 같이 3개의 화면으로 이루어져있습니다. 그리고 각 화면이동간에 커스텀 anim이 구현되어 있습니다. 또한 넘길 arguments들도 설정되어 있습니다.

또한 커스텀 애니메이션이 구현되어있는 것을 볼 수 있습니다.

### HomeViewPagerFragment(홈 식물 리스트 프래그먼트 화면) -> PantDetailFragment(식물 상세화면)
하나만 예시를 들겠습니다.

nav_graph.xml 을 보면 PlantDetailFragment 는 plantId라는 String 타입 매개변수를 전달받습니다.

그리고 HomeViewPager에서는 \<action> 으로 PantDetailFragment 로 Direction이 설정되어 있습니다. 이렇게 설정해놓으면 빌드시 자동으로 NavController에서 네비게이션하는 함수가 만들어집니다.

 ```Kotlin
 //nav_garden.xml
    .
    .
    <fragment
        android:id="@+id/plant_detail_fragment"
        android:name="com.google.samples.apps.sunflower.PlantDetailFragment"
        android:label="@string/plant_details_title"
        tools:layout="@layout/fragment_plant_detail">

        <action
            android:id="@+id/action_plant_detail_fragment_to_gallery_fragment"
            app:destination="@id/gallery_fragment"
            app:enterAnim="@anim/slide_in_right"
            app:exitAnim="@anim/slide_out_left"
            app:popEnterAnim="@anim/slide_in_left"
            app:popExitAnim="@anim/slide_out_right" />
        <argument
            android:name="plantId"
            app:argType="string" />
    </fragment>
    .
    .
```
### HomeViewPagerFragment - PlantListFragment - PlantAdapter
 ```Kotlin
 //PlantAdapter.kotlin
class PlantAdapter : ListAdapter<Plant, RecyclerView.ViewHolder>(PlantDiffCallback()) {

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): RecyclerView.ViewHolder {
        return PlantViewHolder(
            ListItemPlantBinding.inflate(
                LayoutInflater.from(parent.context),
                parent,
                false
            )
        )
    }

    override fun onBindViewHolder(holder: RecyclerView.ViewHolder, position: Int) {
        val plant = getItem(position)
        (holder as PlantViewHolder).bind(plant)
    }

    class PlantViewHolder(
        private val binding: ListItemPlantBinding
    ) : RecyclerView.ViewHolder(binding.root) {
        init {
            binding.setClickListener {
                binding.plant?.let { plant ->
                    navigateToPlant(plant, it)
                }
            }
        }

		// 이 부분을 보면 됩니다. Jetpack Navigation Direction 
        private fun navigateToPlant(
            plant: Plant,
            view: View
        ) {
            val direction =
                HomeViewPagerFragmentDirections.actionViewPagerFragmentToPlantDetailFragment(
                    plant.plantId
                )
            view.findNavController().navigate(direction)
        }

        fun bind(item: Plant) {
            binding.apply {
                plant = item
                executePendingBindings()
            }
        }
    }
}

private class PlantDiffCallback : DiffUtil.ItemCallback<Plant>() {

    override fun areItemsTheSame(oldItem: Plant, newItem: Plant): Boolean {
        return oldItem.plantId == newItem.plantId
    }

    override fun areContentsTheSame(oldItem: Plant, newItem: Plant): Boolean {
        return oldItem == newItem
    }
}
```
ViewPager 안의 PlantListFragment 리사이클러뷰의 PlantAdater 에 아이템 클릭시 식물 상세화면으로 가는 이벤트가 구현되어 있습니다.
Jetpack Navigation Graph에 의해 자동으로 만들어진 Directions 객체와 action 함수를 통해 프래그먼트간 전환 및 값 전달을 하게 되는 것을 볼 수 있습니다.

![image](https://user-images.githubusercontent.com/48902047/149468142-9c35d991-8c00-4134-b10f-4446af60b5ca.png)

### PlantDetailFragment
by navArgs 로 값을 전달받는 것을 볼 수 있습니다.

 ```Kotlin
 //PlantDetailFragment.kotlin
@AndroidEntryPoint
class PlantDetailFragment : Fragment() {

    private val args: PlantDetailFragmentArgs by navArgs()

    @Inject
    lateinit var plantDetailViewModelFactory: PlantDetailViewModelFactory

    private val plantDetailViewModel: PlantDetailViewModel by viewModels {
        PlantDetailViewModel.provideFactory(plantDetailViewModelFactory, args.plantId)
    }
```
![image](https://user-images.githubusercontent.com/48902047/149468282-0dd3aae8-593d-44bc-a1d2-cb08dad3b0b1.png)









