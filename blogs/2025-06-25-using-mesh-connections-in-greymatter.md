---
title: "Using Mesh Connections in Greymatter"
url: "https://greymatter.io/blog/how-to-enable-mesh-connections-in-greymatter/"
date: "Wed, 25 Jun 2025 18:12:20 +0000"
author: "TJ"
feed_url: "https://greymatter.io/feed/"
---
<h2 class="wp-block-heading" id="h-a-hands-on-guide-for-developers-managing-multi-mesh-environments"><strong>A Hands-On Guide for Developers Managing Multi-Mesh Environments</strong></h2>



<p>Managing multiple service meshes across clusters, regions, or clouds introduces significant operational complexity for platform and DevOps teams. Greymatter Mesh Connections eliminates these challenges with end-to-end automation, zero trust security, and instant observability—enabling encrypted, policy-aware inter-mesh connectivity and failover, all orchestrated through declarative GitOps workflows.</p>



<p>When two or more Greymatter and Istio meshes connect, they follow a secure, automated handshake to ensure only trusted parties communicate:</p>



<ul class="wp-block-list">
<li>Both Catalog A and Bridge Edge B start by loading their own security certificates and keys. Each also loads the other’s certificate for trust.</li>



<li>Bridge Edge B loads a whitelist to control which catalogs are allowed to connect.</li>



<li>Catalog A opens a connection to Bridge Edge B. Bridge Edge B checks Catalog A’s certificate for authenticity.</li>



<li>Bridge Edge B sends its own certificate back, and Catalog A verifies it.</li>



<li>Once both sides trust each other, Catalog A requests the mesh configuration.</li>



<li>Bridge Edge B double-checks Catalog A against the whitelist, gathers the needed mesh data, and sends the configuration back.</li>
</ul>



<p>This process ensures that only authorized catalogs can connect, and all communication is encrypted and verified. See Figure 1 below for the handshake sequence.</p>



<figure class="wp-block-image size-full"><img alt="" class="wp-image-7384" height="1081" src="https://greymatter.io/wp-content/uploads/Square-How-To.png" width="1081" /></figure>



<p class="has-text-align-center"><strong>Figure 1</strong></p>



<p>This guide provides a practical, step-by-step approach to enabling Mesh Connections, setting up multi-cloud failover, and synchronizing services from a centralized repository, empowering you to deliver resilient, compliant, and scalable architectures.</p>



<h2 class="wp-block-heading has-x-large-font-size" id="h-why-mesh-connections-with-greymatter"><strong>Why Mesh Connections with Greymatter?</strong></h2>



<ul class="wp-block-list">
<li><strong>Unified Multicloud Connectivity:</strong> Seamlessly connect services across AWS, Azure, GCP, on-prem, and sovereign clouds—no brittle integrations, just one platform.</li>



<li><strong>Workload-Centric Zero Trust</strong>: Enforce NIST Zero Trust and FIPS compliance automatically, with mTLS at every hop and workload identity baked in.</li>



<li><strong>Instant Observability:</strong> Gain forensic-level audits, real-time telemetry, and automatic compliance checks—no blind spots, no manual tuning.</li>



<li><strong>Automated GitOps Workflows:</strong> Prevent configuration drift and accelerate deployments with declarative, version-controlled policies.</li>
</ul>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading has-x-large-font-size" id="h-step-1-set-up-your-certificates"><strong>Step 1: Set Up Your Certificates</strong></h2>



<p>Establish public mTLS between meshes for secure, zero trust communication across clusters.</p>



<p><em>Recommendation: Use CA-signed certificates for production.</em></p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading has-x-large-font-size" id="h-step-2-enable-incoming-connections-on-mesh-b"><strong>Step 2: Enable Incoming Connections on Mesh B</strong></h2>



<p>In Mesh B’s config.cue, enable mesh connections and define secure inbound sockets:</p>



<div class="wp-block-kevinbatdorf-code-block-pro"><span><svg height="14" viewBox="0 0 54 14" width="54" xmlns="http://www.w3.org/2000/svg"><g fill="none" fill-rule="evenodd" transform="translate(1 1)"><circle cx="6" cy="6" fill="#FF5F56" r="6" stroke="#E0443E" stroke-width=".5"></circle><circle cx="26" cy="6" fill="#FFBD2E" r="6" stroke="#DEA123" stroke-width=".5"></circle><circle cx="46" cy="6" fill="#27C93F" r="6" stroke="#1AAB29" stroke-width=".5"></circle></g></svg></span><span class="code-block-pro-copy-button" style="color: #d8dee9ff; display: none;" tabindex="0"><pre class="code-block-pro-copy-button-pre"><textarea class="code-block-pro-copy-button-textarea" readonly="readonly" tabindex="-1">defaults: {
  mesh_connections_enabled: true
}

#AcceptConnections

#Connections &amp; {
  inbound_socket: {
    tls: #InboundTLSConfig
  }
}</textarea></pre><svg fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path class="with-check" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-6 9l2 2 4-4" stroke-linecap="round" stroke-linejoin="round"></path><path class="without-check" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" stroke-linecap="round" stroke-linejoin="round"></path></svg></span><pre class="shiki nord" style="background-color: #2e3440ff;" tabindex="0"><code><span class="line"><span style="color: #D8DEE9FF;">defaults: </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">  </span><span style="color: #D8DEE9;">mesh_connections_enabled</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #81A1C1;">true</span></span>
<span class="line"><span style="color: #ECEFF4;">}</span></span>
<span class="line"></span>
<span class="line"><span style="color: #D8DEE9FF;">#AcceptConnections</span></span>
<span class="line"></span>
<span class="line"><span style="color: #D8DEE9FF;">#Connections &amp; </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">  </span><span style="color: #D8DEE9;">inbound_socket</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">    </span><span style="color: #D8DEE9;">tls</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #D8DEE9;">#InboundTLSConfig</span></span>
<span class="line"><span style="color: #D8DEE9FF;">  </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #ECEFF4;">}</span></span></code></pre></div>



<p>This configuration allows Mesh A services to securely discover and interact with Mesh B services, enforcing policy and encryption at every layer.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading has-x-large-font-size" id="h-step-3-configure-outgoing-connections-on-mesh-a"><strong>Step 3: Configure Outgoing Connections on Mesh A</strong></h2>



<p>In Mesh A’s config.cue, define the outbound connection:</p>



<div class="wp-block-kevinbatdorf-code-block-pro"><span><svg height="14" viewBox="0 0 54 14" width="54" xmlns="http://www.w3.org/2000/svg"><g fill="none" fill-rule="evenodd" transform="translate(1 1)"><circle cx="6" cy="6" fill="#FF5F56" r="6" stroke="#E0443E" stroke-width=".5"></circle><circle cx="26" cy="6" fill="#FFBD2E" r="6" stroke="#DEA123" stroke-width=".5"></circle><circle cx="46" cy="6" fill="#27C93F" r="6" stroke="#1AAB29" stroke-width=".5"></circle></g></svg></span><span class="code-block-pro-copy-button" style="color: #d8dee9ff; display: none;" tabindex="0"><pre class="code-block-pro-copy-button-pre"><textarea class="code-block-pro-copy-button-textarea" readonly="readonly" tabindex="-1">defaults: {
  mesh_connections_enabled: true
}

#Connections &amp; {
  connections: {
    "mesh-b": {
      mesh_catalog_api: "https://mesh-b-catalog.example.com:10710"
      greymatter_public_url: "https://mesh-b.example.com"
      tls: #OutboundTLSConfig &amp; {
        ssl_config: {
          sni: "mesh-b.example.com"
          verify_certificate_spki:[&lt;fingerprint>]
          verify_certificate_hash:[&lt;fingerprint>]
        }
      }
    }
  }
}</textarea></pre><svg fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path class="with-check" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-6 9l2 2 4-4" stroke-linecap="round" stroke-linejoin="round"></path><path class="without-check" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" stroke-linecap="round" stroke-linejoin="round"></path></svg></span><pre class="shiki nord" style="background-color: #2e3440ff;" tabindex="0"><code><span class="line"><span style="color: #D8DEE9FF;">defaults: </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">  </span><span style="color: #D8DEE9;">mesh_connections_enabled</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #81A1C1;">true</span></span>
<span class="line"><span style="color: #ECEFF4;">}</span></span>
<span class="line"></span>
<span class="line"><span style="color: #D8DEE9FF;">#Connections &amp; </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">  </span><span style="color: #D8DEE9;">connections</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">    </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #8FBCBB;">mesh-b</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #D8DEE9;">mesh_catalog_api</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">https://mesh-b-catalog.example.com:10710</span><span style="color: #ECEFF4;">&quot;</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #D8DEE9;">greymatter_public_url:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">https://mesh-b.example.com</span><span style="color: #ECEFF4;">&quot;</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #D8DEE9;">tls:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #D8DEE9;">#OutboundTLSConfig</span><span style="color: #D8DEE9FF;"> </span><span style="color: #D8DEE9;">&amp;</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #D8DEE9;">ssl_config</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">          </span><span style="color: #D8DEE9;">sni</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">mesh-b.example.com</span><span style="color: #ECEFF4;">&quot;</span></span>
<span class="line"><span style="color: #D8DEE9FF;">          </span><span style="color: #D8DEE9;">verify_certificate_spki:</span><span style="color: #ECEFF4;">[</span><span style="color: #D8DEE9;">&lt;fingerprint&gt;</span><span style="color: #ECEFF4;">]</span></span>
<span class="line"><span style="color: #D8DEE9FF;">          </span><span style="color: #D8DEE9;">verify_certificate_hash:</span><span style="color: #ECEFF4;">[</span><span style="color: #D8DEE9;">&lt;fingerprint&gt;</span><span style="color: #ECEFF4;">]</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">    </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">  </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #ECEFF4;">}</span></span></code></pre></div>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading has-x-large-font-size" id="h-step-4-deploy-bridge-services-for-traffic-failover"><strong>Step 4: Deploy Bridge Services for Traffic Failover</strong></h2>



<p>Deploy a bridge.yaml to each mesh to enable north-south traffic and failover capabilities.</p>



<ul class="wp-block-list">
<li>Expose an ingress listener (default 10908 or 9443)</li>



<li>Define upstreams and listeners with #HealthCheckFilter</li>



<li>Configure impersonation proxies to route traffic to backup services</li>
</ul>



<p>You can configure Greymatter to impersonate a backend service from another mesh and reroute traffic when the primary backend fails.</p>



<p>Example route config:</p>



<div class="wp-block-kevinbatdorf-code-block-pro"><span><svg height="14" viewBox="0 0 54 14" width="54" xmlns="http://www.w3.org/2000/svg"><g fill="none" fill-rule="evenodd" transform="translate(1 1)"><circle cx="6" cy="6" fill="#FF5F56" r="6" stroke="#E0443E" stroke-width=".5"></circle><circle cx="26" cy="6" fill="#FFBD2E" r="6" stroke="#DEA123" stroke-width=".5"></circle><circle cx="46" cy="6" fill="#27C93F" r="6" stroke="#1AAB29" stroke-width=".5"></circle></g></svg></span><span class="code-block-pro-copy-button" style="color: #d8dee9ff; display: none;" tabindex="0"><pre class="code-block-pro-copy-button-pre"><textarea class="code-block-pro-copy-button-textarea" readonly="readonly" tabindex="-1">routes: {
  "/backend": {
    upstream: #Upstream &amp; {
      name: "bridge",
      namespace: context.globals.namespace
    },
    health_check: {
      timeout_ms: 1000,
      interval_ms: 2000,
      unhealthy_threshold: 2,
      healthy_threshold: 5,
      http: {
        path: "/health"
      }
    }
  }
}</textarea></pre><svg fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path class="with-check" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-6 9l2 2 4-4" stroke-linecap="round" stroke-linejoin="round"></path><path class="without-check" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" stroke-linecap="round" stroke-linejoin="round"></path></svg></span><pre class="shiki nord" style="background-color: #2e3440ff;" tabindex="0"><code><span class="line"><span style="color: #D8DEE9FF;">routes: </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">  </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #8FBCBB;">/backend</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">    </span><span style="color: #D8DEE9;">upstream</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #D8DEE9;">#Upstream</span><span style="color: #D8DEE9FF;"> </span><span style="color: #D8DEE9;">&amp;</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #D8DEE9;">name</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">bridge</span><span style="color: #ECEFF4;">&quot;</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #D8DEE9;">namespace</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #D8DEE9;">context.globals.namespace</span></span>
<span class="line"><span style="color: #D8DEE9FF;">    </span><span style="color: #ECEFF4;">},</span></span>
<span class="line"><span style="color: #D8DEE9FF;">    </span><span style="color: #D8DEE9;">health_check</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #D8DEE9;">timeout_ms</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">1000</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #D8DEE9;">interval_ms</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">2000</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #D8DEE9;">unhealthy_threshold</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">2</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #D8DEE9;">healthy_threshold</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #B48EAD;">5</span><span style="color: #ECEFF4;">,</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #D8DEE9;">http</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">{</span></span>
<span class="line"><span style="color: #D8DEE9FF;">        </span><span style="color: #D8DEE9;">path</span><span style="color: #ECEFF4;">:</span><span style="color: #D8DEE9FF;"> </span><span style="color: #ECEFF4;">&quot;</span><span style="color: #A3BE8C;">/health</span><span style="color: #ECEFF4;">&quot;</span></span>
<span class="line"><span style="color: #D8DEE9FF;">      </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">    </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #D8DEE9FF;">  </span><span style="color: #ECEFF4;">}</span></span>
<span class="line"><span style="color: #ECEFF4;">}</span></span></code></pre></div>



<p>Greymatter can impersonate backend services from another mesh, automatically rerouting traffic if the primary backend fails—enabling seamless, policy-driven failover.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading has-x-large-font-size" id="h-step-5-centralize-policy-via-gitops"><strong>Step 5: Centralize Policy via GitOps</strong></h2>



<p>Use a shared Git repository to manage configuration for multiple meshes.</p>



<ul class="wp-block-list">
<li>Commit to a common branch (e.g., main or multi-cloud-sync)</li>



<li>Let greymatter pull and apply changes</li>



<li>Validate it is working through Sense UI, Lens, or command line</li>
</ul>



<p>Greymatter supports:</p>



<ul class="wp-block-list">
<li>Blue/Green deployments with weighted traffic</li>



<li>Namespace segmentation</li>



<li>Shared tagging of services (e.g., east, west, prod, test)</li>
</ul>



<p><em>Pro Tip: Greymatter’s declarative state management prevents drift and accelerates compliance audits.</em></p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading has-x-large-font-size" id="h-real-example-multicloud-multi-region-deployment"><strong>Real Example: Multicloud, Multi-Region Deployment</strong></h2>



<p>A platform team deployed an application across Azure AKS and AWS EKS, managed by a central Git repository with GSL-based policy and configuration. They gradually shifted traffic from 50/50 to 100% green. When the ASK mesh failed, traffic seamlessly failed over to the EKS mesh.</p>



<p>Metrics achieved:</p>



<ul class="wp-block-list">
<li><strong>99% reduction</strong> in implementation time compared to manual configurations</li>



<li><strong>60% lower total cost</strong> of ownership versus open source alternatives</li>



<li><strong>95-99% reduction</strong> in outages</li>



<li><strong>NIST compliance</strong> alignment completed in under a month</li>
</ul>



<p>Each deployment:</p>



<ul class="wp-block-list">
<li>Verified commit hash and branch consistency</li>



<li>Completed autonomous provisioning of all necessary application networking tooling</li>



<li>Passed health checks in the Sense UI</li>
</ul>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading has-x-large-font-size">Security Considerations</h2>



<ul class="wp-block-list">
<li>All mesh connections enforce NIST/DoD Zero Trust with mTLS and workload identity.</li>



<li>RBAC and policy orchestration are managed centrally and applied automatically across all meshes.</li>



<li>Every connection, route, and policy is auditable and version-controlled.</li>
</ul>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading has-x-large-font-size" id="h-already-built-into-greymatter"><strong>Already Built Into Greymatter</strong></h2>



<p>Greymatter Mesh Connections includes:</p>



<ul class="wp-block-list">
<li>Automatic Mesh Discovery and Registration</li>



<li>Multi-Region Failover Routing</li>



<li>Cross-Mesh Policy Orchestration</li>



<li>Dynamic Topology View in Sense</li>
</ul>



<p>Greymatter keeps Mesh B read-only from Mesh A to preserve ownership while providing full visibility and control for routing and policies.</p>



<hr class="wp-block-separator has-alpha-channel-opacity" />



<h2 class="wp-block-heading has-x-large-font-size" id="h-next-steps"><strong>Next Steps</strong></h2>



<p>Mesh Connections empowers your platform team to manage services securely and automatically across cloud, region, and network boundaries—without reinventing your workflows.</p>



    <div class="block-calendly-embed wp-block-buttons has-small-font-size is-layout-flex wp-block-buttons-is-layout-flex is-content-justification-center">
              <div class="wp-block-button">
          <!-- Calendly link widget begin -->


<a href="">Receive a Technical Deep Dive</a>
<!-- Calendly link widget end -->         </div>
              <div class="wp-block-button">
          <a href="https://greymatter.io/contact-us/">Request a Demo</a>        </div>
              <div class="wp-block-button">
          <a href="https://greymatter.io/start-for-free/">Get a 30-Day Proof of Concept</a>        </div>
          </div>
  <p>The post <a href="https://greymatter.io/blog/how-to-enable-mesh-connections-in-greymatter/">Using Mesh Connections in Greymatter</a> appeared first on <a href="https://greymatter.io">Greymatter.io</a>.</p>
