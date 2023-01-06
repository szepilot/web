
# Welcome

 
 For all $$x$$ and $$y$$ in $$\mathbb{R}^k$$ it is true 
 
 
 A szabályzási lánconál a következő jelöléseket használjuk:
- $$r(t)$$: a célérték, avagy referenciajel
- $$y(t)$$: a visszacsatolt érték.
- $$m(t)$$: a valós rendszeren mért érték. Megjegyzés: sokszor az érzékelőt ideálisnak tekintjük, így $$y(t)=m(t)$$, és így a visszacsatolt érték egyben az absztrakt rendszer kimenete.
- $$e(t)$$: hibajel, a szabályzó bemenete.
- $$i(t)$$: a szabályzó által meghatározott beavatkozó jel.
- $$f(t)$$: előrecsatolt ág.
- $$u(t)$$: a rendszer bemenete
- $$d(t)$$: külső zavarok.
 
 
 🚗
 ✅
 🔴
 
 
<table>
<tr>
<th>ROS1</th>
<th>ROS2</th>
</tr>
<tr>
<td> 
 
 
 ``` cpp
// AA
ros::init(argc, argv, "lidar_filt");
ros::NodeHandle nh;
// Create a ROS subscriber for the input point cloud
ros::Subscriber sub = nh.subscribe("points_raw", 1, cloud_cb);

// Create a ROS publisher for the output point cloud
pub = nh.advertise<pcl::PCLPointCloud2>("points_filt", 1);

// Spin
ros::spin();
```
 
</td>
<td>



``` cpp
// Initialize ROS
ros::init(argc, argv, "lidar_filt");
ros::NodeHandle nh;
// Create a ROS subscriber for the input point cloud
ros::Subscriber sub = nh.subscribe("points_raw", 1, cloud_cb);

// Create a ROS publisher for the output point cloud
pub = nh.advertise<pcl::PCLPointCloud2>("points_filt", 1);

// Spin
ros::spin();
```

</td>
</tr>
</table>
 
 
``` cpp
#include <ros/ros.h>
#include <pcl_conversions/pcl_conversions.h>
#include <pcl/point_cloud.h>
#include <pcl/point_types.h>
#include <pcl/filters/voxel_grid.h>
#include <pcl/filters/crop_box.h>

ros::Publisher pub;
ros::Publisher marker_pub;

void cloud_cb(const pcl::PCLPointCloud2ConstPtr &cloud)
{
    pcl::PCLPointCloud2 cloud_filtered;

    // Define min and max for X, Y and Z
    float minX = 0.0, minY = -1.0, minZ = -1.384;
    float maxX = 16.0, maxY = +1.0, maxZ = -0.15;

    pcl::CropBox<pcl::PCLPointCloud2> boxFilter;
    boxFilter.setMin(Eigen::Vector4f(minX, minY, minZ, 1.0));
    boxFilter.setMax(Eigen::Vector4f(maxX, maxY, maxZ, 1.0));
    boxFilter.setInputCloud(cloud);
    boxFilter.filter(cloud_filtered);
    if(cloud_filtered.data.size() > 10) 
        ROS_WARN_STREAM("Object in the selected area");

    // Publish the filtered LIDAR data
    pub.publish(cloud_filtered);

}

int main(int argc, char **argv)
{
    // Initialize ROS
    ros::init(argc, argv, "lidar_filt");
    ros::NodeHandle nh;
    // Create a ROS subscriber for the input point cloud
    ros::Subscriber sub = nh.subscribe("points_raw", 1, cloud_cb);

    // Create a ROS publisher for the output point cloud
    pub = nh.advertise<pcl::PCLPointCloud2>("points_filt", 1);

    // Spin
    ros::spin();
}
```
