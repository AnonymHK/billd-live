<template>
  <div class="my-wrap">
    <div class="id">用户id：{{ userStore.userInfo?.id }}</div>
    <div class="avatar">
      <span class="txt">用户头像：</span>
      <Avatar
        :url="userStore.userInfo?.avatar"
        :name="userStore.userInfo?.username"
        :size="30"
      ></Avatar>
    </div>
    <div>用户昵称：{{ userStore.userInfo?.username }}</div>
    <div>
      用户角色：{{
        userStore.userInfo?.roles?.map((item) => item.role_name).join(',')
      }}
    </div>
    <div>注册时间：{{ userStore.userInfo?.created_at }}</div>

    <br />
    <div class="pull-url">
      <span
        v-if="!userStore.userInfo?.live_rooms?.length"
        class="link"
        @click="openLiveRoom"
      >
        未开通
      </span>
      <div v-else>
        <div>
          直播间地址：
          <a
            :href="getLiveRoomPageUrl(userStore.userInfo?.live_rooms?.[0]?.id!)"
            class="link"
            target="_blank"
          >
            {{ getLiveRoomPageUrl(userStore.userInfo?.live_rooms?.[0]?.id!) }}
          </a>
        </div>
        <div>直播间标题：{{ userStore.userInfo?.live_rooms?.[0]?.title }}</div>
        <div>
          直播间简介：{{
            userStore.userInfo?.live_rooms?.[0]?.desc || '暂无简介'
          }}
        </div>
        <div>
          直播间分区：{{
            userStore.userInfo?.live_rooms?.[0]?.areas?.[0]?.name || '暂无分区'
          }}
        </div>
        <div>
          开通时间：{{ userStore.userInfo?.live_rooms?.[0]?.created_at }}
        </div>

        <div
          v-if="
            userStore.userInfo?.auths?.find(
              (v) => v.auth_value === DEFAULT_AUTH_INFO.LIVE_PUSH.auth_value
            )
          "
          v-loading="updateKeyLoading"
          class="url-wrap"
        >
          <div
            class="link"
            @click="handleUpdateKey"
          >
            更新地址
          </div>

          <div class="srs">
            <div>
              <span>
                RTMP推流地址：{{
                  userStore.userInfo?.live_rooms?.[0]?.push_rtmp_url!
                }}，
              </span>
              <span
                class="link"
                @click="
                  handleCopy(
                    userStore.userInfo?.live_rooms?.[0]?.push_rtmp_url!
                  )
                "
              >
                复制
              </span>
            </div>
            <div>
              <span
                >OBS服务器：{{
                  userStore.userInfo?.live_rooms?.[0]?.push_obs_server!
                }}，</span
              >
              <span
                class="link"
                @click="
                  handleCopy(
                    userStore.userInfo?.live_rooms?.[0]?.push_obs_server!
                  )
                "
              >
                复制
              </span>
            </div>
            <div>
              <span
                >OBS推流码：{{
                  userStore.userInfo?.live_rooms?.[0]?.push_obs_stream_key!
                }}，</span
              >
              <span
                class="link"
                @click="
                  handleCopy(
                    userStore.userInfo?.live_rooms?.[0]?.push_obs_stream_key!
                  )
                "
              >
                复制
              </span>
            </div>
          </div>

          <br />

          <div>
            CDN直播：
            <div
              v-if="
                userStore.userInfo?.auths?.find(
                  (v) =>
                    v.auth_value === DEFAULT_AUTH_INFO.LIVE_PUSH_CDN.auth_value
                )
              "
              class="cdn"
            >
              <div>
                <span>
                  RTMP推流地址（CDN）：{{
                    userStore.userInfo?.live_rooms?.[0]?.push_cdn_rtmp_url!
                  }}，
                </span>
                <span
                  class="link"
                  @click="
                    handleCopy(
                      userStore.userInfo?.live_rooms?.[0]?.push_cdn_rtmp_url!
                    )
                  "
                >
                  复制
                </span>
              </div>
              <div>
                <span>
                  OBS服务器（CDN）：{{
                    userStore.userInfo?.live_rooms?.[0]?.push_cdn_obs_server!
                  }}，
                </span>
                <span
                  class="link"
                  @click="
                    handleCopy(
                      userStore.userInfo?.live_rooms?.[0]?.push_cdn_obs_server!
                    )
                  "
                >
                  复制
                </span>
              </div>
              <div>
                <span>
                  OBS推流码（CDN）：{{
                    userStore.userInfo?.live_rooms?.[0]
                      ?.push_cdn_obs_stream_key!
                  }}，
                </span>
                <span
                  class="link"
                  @click="
                    handleCopy(
                      userStore.userInfo?.live_rooms?.[0]
                        ?.push_cdn_obs_stream_key!
                    )
                  "
                >
                  复制
                </span>
              </div>
            </div>
            <div
              v-else
              class="link"
              @click="router.push({ name: routerName.author })"
            >
              请联系作者开通~
            </div>
          </div>

          <div>
            转推b站：
            <div
              v-if="
                userStore.userInfo?.auths?.find(
                  (v) =>
                    v.auth_value ===
                    DEFAULT_AUTH_INFO.LIVE_PUSH_FORWARD_BILIBILI.auth_value
                )
              "
              class="cdn"
            >
              <n-input-group>
                <n-input
                  v-model:value="liveRoomInfo!.forward_bilibili_url"
                  style="width: 500px"
                  type="text"
                  placeholder="请输入转推b站url"
                />

                <n-button
                  type="primary"
                  ghost
                  @click="handleUpdateMyLiveRoom()"
                >
                  更新
                </n-button>
              </n-input-group>
            </div>
            <div
              v-else
              class="link"
              @click="router.push({ name: routerName.author })"
            >
              请联系作者开通~
            </div>
          </div>

          <div>
            转推虎牙：
            <div
              v-if="
                userStore.userInfo?.auths?.find(
                  (v) =>
                    v.auth_value ===
                    DEFAULT_AUTH_INFO.LIVE_PUSH_FORWARD_HUYA.auth_value
                )
              "
              class="cdn"
            >
              <n-input-group>
                <n-input
                  v-model:value="liveRoomInfo!.forward_huya_url"
                  style="width: 500px"
                  type="text"
                  placeholder="请输入转推虎牙url"
                />

                <n-button
                  type="primary"
                  ghost
                  @click="handleUpdateMyLiveRoom()"
                >
                  更新
                </n-button>
              </n-input-group>
            </div>
            <div
              v-else
              class="link"
              @click="router.push({ name: routerName.author })"
            >
              请联系作者开通~
            </div>
          </div>

          <div>
            转推斗鱼：
            <div
              v-if="
                userStore.userInfo?.auths?.find(
                  (v) =>
                    v.auth_value ===
                    DEFAULT_AUTH_INFO.LIVE_PUSH_FORWARD_DOUYU.auth_value
                )
              "
              class="cdn"
            >
              <n-input-group>
                <n-input
                  v-model:value="liveRoomInfo!.forward_douyu_url"
                  style="width: 500px"
                  type="text"
                  placeholder="请输入转推斗鱼url"
                />

                <n-button
                  type="primary"
                  ghost
                  @click="handleUpdateMyLiveRoom()"
                >
                  更新
                </n-button>
              </n-input-group>
            </div>
            <div
              v-else
              class="link"
              @click="router.push({ name: routerName.author })"
            >
              请联系作者开通~
            </div>
          </div>

          <div>
            转推抖音：
            <div
              v-if="
                userStore.userInfo?.auths?.find(
                  (v) =>
                    v.auth_value ===
                    DEFAULT_AUTH_INFO.LIVE_PUSH_FORWARD_DOUYIN.auth_value
                )
              "
              class="cdn"
            >
              <n-input-group>
                <n-input
                  v-model:value="liveRoomInfo!.forward_douyin_url"
                  style="width: 500px"
                  type="text"
                  placeholder="请输入转推抖音url"
                />

                <n-button
                  type="primary"
                  ghost
                  @click="handleUpdateMyLiveRoom()"
                >
                  更新
                </n-button>
              </n-input-group>
            </div>
            <div
              v-else
              class="link"
              @click="router.push({ name: routerName.author })"
            >
              请联系作者开通~
            </div>
          </div>

          <div>
            转推小红书：
            <div
              v-if="
                userStore.userInfo?.auths?.find(
                  (v) =>
                    v.auth_value ===
                    DEFAULT_AUTH_INFO.LIVE_PUSH_FORWARD_XIAOHONGSHU.auth_value
                )
              "
              class="cdn"
            >
              <n-input-group>
                <n-input
                  v-model:value="liveRoomInfo!.forward_xiaohongshu_url"
                  style="width: 500px"
                  type="text"
                  placeholder="请输入转推小红书url"
                />

                <n-button
                  type="primary"
                  ghost
                  @click="handleUpdateMyLiveRoom()"
                >
                  更新
                </n-button>
              </n-input-group>
            </div>
            <div
              v-else
              class="link"
              @click="router.push({ name: routerName.author })"
            >
              请联系作者开通~
            </div>
          </div>

          <div>
            转推快手：
            <div
              v-if="
                userStore.userInfo?.auths?.find(
                  (v) =>
                    v.auth_value ===
                    DEFAULT_AUTH_INFO.LIVE_PUSH_FORWARD_KUAISHOU.auth_value
                )
              "
              class="cdn"
            >
              <n-input-group>
                <n-input
                  v-model:value="liveRoomInfo!.forward_kuaishou_url"
                  style="width: 500px"
                  type="text"
                  placeholder="请输入转推快手url"
                />

                <n-button
                  type="primary"
                  ghost
                  @click="handleUpdateMyLiveRoom()"
                >
                  更新
                </n-button>
              </n-input-group>
            </div>
            <div
              v-else
              class="link"
              @click="router.push({ name: routerName.author })"
            >
              请联系作者开通~
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { copyToClipBoard, openToTarget } from 'billd-utils';
import { ref, watch } from 'vue';
import { useRouter } from 'vue-router';

import { fetchUpdateLiveRoomKey, fetchUpdateMyLiveRoom } from '@/api/liveRoom';
import { DEFAULT_AUTH_INFO, URL_QUERY } from '@/constant';
import { loginTip } from '@/hooks/use-login';
import { routerName } from '@/router';
import { useUserStore } from '@/store/user';
import { ILiveRoom, LiveRoomTypeEnum } from '@/types/ILiveRoom';
import { getLiveRoomPageUrl } from '@/utils';

const userStore = useUserStore();
const router = useRouter();
const liveRoomInfo = ref<ILiveRoom>();

const updateKeyLoading = ref(false);

function handleCopy(url: string) {
  copyToClipBoard(url);
  window.$message.success('复制成功！');
}

watch(
  () => userStore.userInfo,
  (newval) => {
    if (newval?.live_rooms?.[0]) {
      liveRoomInfo.value = newval?.live_rooms[0];
    }
  },
  {
    immediate: true,
  }
);

async function handleUpdateMyLiveRoom() {
  const res = await fetchUpdateMyLiveRoom({
    forward_bilibili_url: liveRoomInfo.value?.forward_bilibili_url,
    forward_douyin_url: liveRoomInfo.value?.forward_douyin_url,
    forward_douyu_url: liveRoomInfo.value?.forward_douyu_url,
    forward_huya_url: liveRoomInfo.value?.forward_huya_url,
    forward_kuaishou_url: liveRoomInfo.value?.forward_kuaishou_url,
    forward_xiaohongshu_url: liveRoomInfo.value?.forward_xiaohongshu_url,
  });
  if (res.code === 200) {
    window.$message.success('修改成功！');
  }
}

function openLiveRoom() {
  if (!loginTip()) {
    return;
  }
  const url = router.resolve({
    name: routerName.push,
    query: { [URL_QUERY.liveType]: LiveRoomTypeEnum.srs },
  });
  openToTarget(url.href);
}

async function handleUpdateKey() {
  try {
    updateKeyLoading.value = true;
    const res = await fetchUpdateLiveRoomKey();
    if (res.code === 200 && userStore.userInfo?.live_rooms?.[0]) {
      userStore.userInfo.live_rooms[0].push_obs_server =
        res.data.srsPushRes.obs_server;
      userStore.userInfo.live_rooms[0].push_obs_stream_key =
        res.data.srsPushRes.obs_stream_key;
      userStore.userInfo.live_rooms[0].push_rtmp_url =
        res.data.srsPushRes.rtmp_url;
      userStore.userInfo.live_rooms[0].push_srt_url =
        res.data.srsPushRes.srt_url;
      userStore.userInfo.live_rooms[0].push_webrtc_url =
        res.data.srsPushRes.webrtc_url;
      userStore.userInfo.live_rooms[0].push_cdn_obs_server =
        res.data.cdnPushRes.obs_server;
      userStore.userInfo.live_rooms[0].push_cdn_obs_stream_key =
        res.data.cdnPushRes.obs_stream_key;
      userStore.userInfo.live_rooms[0].push_cdn_rtmp_url =
        res.data.cdnPushRes.rtmp_url;
      userStore.userInfo.live_rooms[0].push_cdn_srt_url =
        res.data.cdnPushRes.srt_url;
      userStore.userInfo.live_rooms[0].push_cdn_webrtc_url =
        res.data.cdnPushRes.webrtc_url;
    }
  } catch (error) {
    console.error(error);
  } finally {
    updateKeyLoading.value = false;
  }
}
</script>

<style lang="scss" scoped>
.my-wrap {
  position: relative;
  padding: 10px;
  .link {
    display: inline-block;
    color: $theme-color-gold;
    text-decoration: none;
    cursor: pointer;

    user-select: none;
  }
  .avatar {
    display: flex;
    align-items: center;
    .txt {
      margin-right: 10px;
    }
  }
  .url-wrap {
    position: relative;
    margin-top: 10px;
    .cdn {
      margin-bottom: 10px;
    }
  }
}
</style>
