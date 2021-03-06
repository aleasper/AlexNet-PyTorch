# AlexNet implementation with PyTorch

### Релизация сверточной нейронной сети [арихтектуры AlexNet](https://en.wikipedia.org/wiki/AlexNet)  

Установка необходимых библиотек:
`pip install -r requirements.txt`

Для режима обучения нейронной сети необходимо заполнить директорию `dataset` соответствующим содержимым
формата `.jpg` или `.png`.

С помощью модуля `torchvision.transforms` изображения конвертируются в тензор размером `[3, 244, 244]`
состоящий из трёх цветовых каналов и соответсвующих им значений пикселов. 

Архитектура нейронной сети представлена следующим кодом, где на вход должен подаваться
тензор размерностью `[1, 3, 224, 224]` состоящий из значений от -1 до 1.
```python
class AlexNet(nn.Module):
    def __init__(self):

        dropout_rate = 0.5
        amount_of_classes = 3

        super(AlexNet, self).__init__()

        self.features = nn.Sequential(
            nn.Conv2d(3, 64, kernel_size=11, stride=4, padding=2),
            nn.ReLU(inplace=True),
            nn.MaxPool2d(kernel_size=3, stride=2),
            nn.Conv2d(64, 192, kernel_size=5, padding=2),
            nn.ReLU(inplace=True),
            nn.MaxPool2d(kernel_size=3, stride=2),
            nn.Conv2d(192, 384, kernel_size=3, padding=1),
            nn.ReLU(inplace=True),
            nn.Conv2d(384, 256, kernel_size=3, padding=1),
            nn.ReLU(inplace=True),
            nn.Conv2d(256, 256, kernel_size=3, padding=1),
            nn.ReLU(inplace=True),
            nn.MaxPool2d(kernel_size=3, stride=2)
        )
        self.avgpool = nn.AdaptiveAvgPool2d((6, 6))
        self.classifier = nn.Sequential(
            nn.Dropout(dropout_rate),
            nn.Linear(256 * 6 * 6, 4096),
            nn.ReLU(inplace=True),
            nn.Dropout(dropout_rate),
            nn.Linear(4096, 4096),
            nn.ReLU(inplace=True),
            nn.Linear(4096, amount_of_classes),
        )
        for m in self.modules():
            if isinstance(m, nn.Conv2d):
                nn.init.kaiming_normal_(m.weight, mode='fan_out', nonlinearity='relu')
                if m.bias is not None:
                    nn.init.constant_(m.bias, 0)
            elif isinstance(m, nn.BatchNorm2d):
                nn.init.constant_(m.weight, 1)
                nn.init.constant_(m.bias, 0)
            elif isinstance(m, nn.Linear):
                nn.init.normal_(m.weight, 0, 0.01)
                nn.init.constant_(m.bias, 0)
```


