---
share: "true"
---
Compose Image组件

```kotlin
package com.tospery.myandroid.samples  
  
import androidx.annotation.DrawableRes  
import androidx.compose.foundation.Image  
import androidx.compose.foundation.background  
import androidx.compose.foundation.border  
import androidx.compose.foundation.layout.Arrangement  
import androidx.compose.foundation.layout.Box  
import androidx.compose.foundation.layout.Column  
import androidx.compose.foundation.layout.Row  
import androidx.compose.foundation.layout.aspectRatio  
import androidx.compose.foundation.layout.fillMaxSize  
import androidx.compose.foundation.layout.fillMaxWidth  
import androidx.compose.foundation.layout.height  
import androidx.compose.foundation.layout.padding  
import androidx.compose.foundation.layout.size  
import androidx.compose.foundation.lazy.LazyColumn  
import androidx.compose.foundation.shape.CircleShape  
import androidx.compose.foundation.shape.RoundedCornerShape  
import androidx.compose.material3.AssistChip  
import androidx.compose.material3.ElevatedCard  
import androidx.compose.material3.MaterialTheme  
import androidx.compose.material3.Text  
import androidx.compose.runtime.Composable  
import androidx.compose.ui.Alignment  
import androidx.compose.ui.Modifier  
import androidx.compose.ui.draw.alpha  
import androidx.compose.ui.draw.clip  
import androidx.compose.ui.graphics.ColorFilter  
import androidx.compose.ui.layout.ContentScale  
import androidx.compose.ui.res.painterResource  
import androidx.compose.ui.unit.dp  
import com.tospery.myandroid.R  
import com.tospery.myandroid.Sample  
  
val ComposeImageSample = Sample(  
    title = "Compose Image组件",  
    sourcePath = "ComposeImageSample.kt",  
    content = { ComposeImageSampleContent() }  
)  
  
@Composable  
private fun ComposeImageSampleContent() {  
    LazyColumn(  
        modifier = Modifier.fillMaxSize(),  
        verticalArrangement = Arrangement.spacedBy(16.dp)  
    ) {  
        item { BasicImageCard() }  
        item { LocalImageGalleryCard() }  
        item { ContentScaleCard() }  
        item { ImageModifierCard() }  
        item { AccessibilityCard() }  
    }  
}  
  
@Composable  
private fun BasicImageCard() {  
    ElevatedCard(  
        modifier = Modifier.fillMaxWidth()  
    ) {  
        Column(  
            modifier = Modifier.padding(20.dp),  
            verticalArrangement = Arrangement.spacedBy(14.dp)  
        ) {  
            Text(  
                text = "本地图片资源",  
                style = MaterialTheme.typography.titleLarge  
            )  
            Text(  
                text = "使用 painterResource 读取 drawable-nodpi 中的图片资源。",  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            Box(  
                modifier = Modifier  
                    .fillMaxWidth()  
                    .height(160.dp)  
                    .clip(RoundedCornerShape(12.dp))  
                    .background(MaterialTheme.colorScheme.surfaceVariant),  
                contentAlignment = Alignment.Center  
            ) {  
                // 背景只用于装饰，contentDescription 传 null，避免读屏重复朗读。  
                Image(  
                    painter = painterResource(id = R.drawable.img_grassland),  
                    contentDescription = null,  
                    modifier = Modifier.matchParentSize(),  
                    contentScale = ContentScale.Crop  
                )  
                // 有实际含义的图片应该提供清晰的 contentDescription。  
                Image(  
                    painter = painterResource(id = R.drawable.icon_avatar_color),  
                    contentDescription = "彩色头像",  
                    modifier = Modifier.size(112.dp)  
                )  
            }  
        }  
    }  
}  
  
@Composable  
private fun LocalImageGalleryCard() {  
    ElevatedCard(  
        modifier = Modifier.fillMaxWidth()  
    ) {  
        Column(  
            modifier = Modifier.padding(20.dp),  
            verticalArrangement = Arrangement.spacedBy(14.dp)  
        ) {  
            Text(  
                text = "本地图片组",  
                style = MaterialTheme.typography.titleLarge  
            )  
            Text(  
                text = "照片类资源放在 drawable-nodpi 中，使用固定容器和 ContentScale.Crop 展示。",  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            Row(  
                horizontalArrangement = Arrangement.spacedBy(10.dp)  
            ) {  
                GalleryImageTile(  
                    label = "人物",  
                    imageResId = R.drawable.img_daughter,  
                    modifier = Modifier.weight(1f)  
                )  
                GalleryImageTile(  
                    label = "城市",  
                    imageResId = R.drawable.img_night_city,  
                    modifier = Modifier.weight(1f)  
                )  
                GalleryImageTile(  
                    label = "建筑",  
                    imageResId = R.drawable.img_tiananmen,  
                    modifier = Modifier.weight(1f)  
                )  
            }  
        }  
    }  
}  
  
@Composable  
private fun GalleryImageTile(  
    label: String,  
    @DrawableRes imageResId: Int,  
    modifier: Modifier = Modifier  
) {  
    Column(  
        modifier = modifier,  
        verticalArrangement = Arrangement.spacedBy(8.dp),  
        horizontalAlignment = Alignment.CenterHorizontally  
    ) {  
        Image(  
            painter = painterResource(id = imageResId),  
            contentDescription = label,  
            modifier = Modifier  
                .fillMaxWidth()  
                .aspectRatio(1f)  
                .clip(RoundedCornerShape(10.dp)),  
            contentScale = ContentScale.Crop  
        )  
        Text(  
            text = label,  
            style = MaterialTheme.typography.labelLarge  
        )  
    }  
}  
  
@Composable  
private fun ContentScaleCard() {  
    ElevatedCard(  
        modifier = Modifier.fillMaxWidth()  
    ) {  
        Column(  
            modifier = Modifier.padding(20.dp),  
            verticalArrangement = Arrangement.spacedBy(14.dp)  
        ) {  
            Text(  
                text = "ContentScale",  
                style = MaterialTheme.typography.titleLarge  
            )  
            Text(  
                text = "contentScale 决定图片如何适配固定尺寸容器。",  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            Row(  
                horizontalArrangement = Arrangement.spacedBy(10.dp)  
            ) {  
                ContentScaleTile(  
                    label = "Fit",  
                    imageResId = R.drawable.img_foreland,  
                    contentScale = ContentScale.Fit,  
                    modifier = Modifier.weight(1f)  
                )  
                ContentScaleTile(  
                    label = "Crop",  
                    imageResId = R.drawable.img_foreland,  
                    contentScale = ContentScale.Crop,  
                    modifier = Modifier.weight(1f)  
                )  
                ContentScaleTile(  
                    label = "Inside",  
                    imageResId = R.drawable.img_foreland,  
                    contentScale = ContentScale.Inside,  
                    modifier = Modifier.weight(1f)  
                )  
            }  
        }  
    }  
}  
  
@Composable  
private fun ContentScaleTile(  
    label: String,  
    @DrawableRes  
    imageResId: Int,  
    contentScale: ContentScale,  
    modifier: Modifier = Modifier  
) {  
    Column(  
        modifier = modifier,  
        verticalArrangement = Arrangement.spacedBy(8.dp),  
        horizontalAlignment = Alignment.CenterHorizontally  
    ) {  
        Box(  
            modifier = Modifier  
                .fillMaxWidth()  
                .aspectRatio(0.8f)  
                .clip(RoundedCornerShape(10.dp))  
                .background(MaterialTheme.colorScheme.surfaceVariant)  
                .border(  
                    width = 1.dp,  
                    color = MaterialTheme.colorScheme.outlineVariant,  
                    shape = RoundedCornerShape(10.dp)  
                )  
        ) {  
            Image(  
                painter = painterResource(id = imageResId),  
                contentDescription = null,  
                modifier = Modifier.matchParentSize(),  
                contentScale = contentScale  
            )  
        }  
        Text(  
            text = label,  
            style = MaterialTheme.typography.labelLarge  
        )  
    }  
}  
  
@Composable  
private fun ImageModifierCard() {  
    ElevatedCard(  
        modifier = Modifier.fillMaxWidth()  
    ) {  
        Column(  
            modifier = Modifier.padding(20.dp),  
            verticalArrangement = Arrangement.spacedBy(14.dp)  
        ) {  
            Text(  
                text = "Modifier 与 ColorFilter",  
                style = MaterialTheme.typography.titleLarge  
            )  
            Text(  
                text = "Image 可以配合裁剪、边框、透明度和着色实现常见视觉状态。",  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            Row(  
                modifier = Modifier.fillMaxWidth(),  
                horizontalArrangement = Arrangement.SpaceEvenly,  
                verticalAlignment = Alignment.CenterVertically  
            ) {  
                Image(  
                    painter = painterResource(id = R.drawable.icon_avatar_woman),  
                    contentDescription = "圆形裁剪头像",  
                    modifier = Modifier  
                        .size(72.dp)  
                        .clip(CircleShape)  
                        .background(MaterialTheme.colorScheme.primaryContainer)  
                        .border(  
                            width = 2.dp,  
                            color = MaterialTheme.colorScheme.primary,  
                            shape = CircleShape  
                        )  
                        .padding(10.dp)  
                )  
                Image(  
                    painter = painterResource(id = R.drawable.icon_collect_selected),  
                    contentDescription = "半透明图标",  
                    modifier = Modifier  
                        .size(72.dp)  
                        .alpha(0.42f)  
                )  
                // colorFilter 常用于单色图标染色或状态强调。  
                Image(  
                    painter = painterResource(id = R.drawable.icon_user),  
                    contentDescription = "着色图标",  
                    modifier = Modifier.size(72.dp),  
                    colorFilter = ColorFilter.tint(MaterialTheme.colorScheme.secondary)  
                )  
            }  
        }  
    }  
}  
  
@Composable  
private fun AccessibilityCard() {  
    ElevatedCard(  
        modifier = Modifier.fillMaxWidth()  
    ) {  
        Column(  
            modifier = Modifier.padding(20.dp),  
            verticalArrangement = Arrangement.spacedBy(12.dp)  
        ) {  
            Text(  
                text = "可访问性建议",  
                style = MaterialTheme.typography.titleLarge  
            )  
            Text(  
                text = "图片承载内容时提供 contentDescription；纯装饰图片使用 null。",  
                style = MaterialTheme.typography.bodyMedium,  
                color = MaterialTheme.colorScheme.onSurfaceVariant  
            )  
            Box(  
                modifier = Modifier  
                    .fillMaxWidth()  
                    .height(180.dp)  
                    .clip(RoundedCornerShape(12.dp))  
                    .background(MaterialTheme.colorScheme.surfaceVariant),  
                contentAlignment = Alignment.Center  
            ) {  
                // 空状态插画是页面内容的一部分，因此这里提供明确描述。  
                Image(  
                    painter = painterResource(id = R.drawable.img_dataempty),  
                    contentDescription = "暂无数据的空状态插画",  
                    modifier = Modifier.size(132.dp)  
                )  
            }  
            Row(  
                horizontalArrangement = Arrangement.spacedBy(10.dp)  
            ) {  
                AssistChip(  
                    onClick = {},  
                    label = { Text(text = "contentDescription") }  
                )  
                AssistChip(  
                    onClick = {},  
                    label = { Text(text = "decorative null") }  
                )  
            }  
        }  
    }  
}
```

