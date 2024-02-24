# A tutorial for using this templete
reference: https://www.bilibili.com/list/watchlater?bvid=BV1UC4y1C73s&oid=794030617

## Use
git clone https://github.com/ashleve/lightning-hydra-template
pip isntall -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple

key: 位置 C:\Users\admin\.netrc
wandb login --relogin
local-1cee3490d48e0c91e268086d707087922438768d
docker run -d -v wandb:/vol -p 8080:8080 --name wandb-local wandb/local

docker wandb停止再开始不好用的话就删除重新运行上面docker run那句话

## Custom develop
For custom develop models and datasets, we just need to focus on such files:
 - 入口文件：src/train.py 对应的配置文件: configs/train.yaml 这个文件一般不用改
 - src/data/custom_datamodule.py 用什么data就改成什么data的信息 
 - src/models/components/your_model.py 存放模型和对应的测试文件，注意最后一层的config.num在配置文件中指定分类数
    ```python
    Class Backbone():
        def __init__(self):
            super().__init__()
            self.xxxx
            ...
            self.xx=nn.Linear(xx, config.num) # config文件位于configs/config.yaml
    
    if __name__ == '__main__':
        from torchviz import make_dot
        import torch
        model = Backbone()
        print(model)
        # batch_size, channels, H, W
        X = torch.randn(1, 3, 32, 32)
        y = model(X)
        make_dot(y.mean(), params=dict(model.named_parameters()))
    ```
 - configs/hparams_search/your_optuna.yaml 该文件可以设置超参数等
    ```python
    ......
    # choose metric which will be optimized by Optuna
    # make sure this is the correct name of some metric logged in lightning module!
    optimized_metric: "val/acc_best"
    ...... 
    # define hyperparameter search space
    params:
        # choise里选表现最好的, 注意会每种可能都遍历跑一遍
        model.net._target_: choice(src.models.components.xxx.Backbone, src.models.components.xxx.Backbone,...)
        model.optimizer._target__: choice(torch.optim.Adam, torch.optim.SGD)
        model_optimizer.lr: choice(0.0001, 0.001, 0.1, xxx)
        data.batch_size: choice(32, 64, xxx)
    ```

 - trainer/default.yaml 中的配置会被trainer/gpu.yaml中的配置覆盖