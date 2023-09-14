<template>
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
        <!-- title -->
        <a class="navbar-brand" href="#">TechpitNotion</a>

        <!-- 画面サイズに応じて折りたたむ設定 -->
        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarText" aria-controls="navbarText" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>

        <!-- メニュー項目 -->
        <div class="collapse navbar-collapse" id="navbarText">
            <ul class="navbar-nav">
                <li class="nav-item">
                    <a class="nav-link" href="#"><i class="fas fa-file-alt"></i>ノートリスト</a>
                </li>

                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" data-toggle="dropdown">
                        <i class="fas fa-user-graduate"></i>
                        学習用ページ
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="#">HTML</a></li>
                        <li><a class="dropdown-item" href="#">CSS</a></li>
                        <li><a class="dropdown-item" href="#">JavaScript</a></li>
                    </ul>
                </li>
            </ul>
        </div>
    </nav>
</template>

<script>
export default {
}
</script>

<style scoped lang="scss">
.navigation-bar {
    height: 60px;
}
</style>