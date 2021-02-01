<script>
import {Auth} from "aws-amplify";

export default {
    name: 'SidebarItems',
    data: () => ({
        userGroup: '',
        adminMenu: [
        {
            _name: 'CSidebarNav',
            _children: [
            {
                _name: 'CSidebarNavItem',
                name: 'Dashboard',
                to: '/dashboard',
                icon: 'cil-speedometer',
            },
            {
                _name: 'CSidebarNavItem',
                name: 'Moorings',
                to: '/moorings',
                icon: 'cil-speedometer',
            },
                {
                _name: 'CSidebarNavItem',
                name: 'Forum',
                to: '/forum',
                icon: 'cil-puzzle',
                badge: {
                    color: 'primary',
                    text: 'NEW'
                }
            },
            ]
        }
        ],
        userMenu: [
                    {
                        _name: 'CSidebarNav',
                        _children: [
                        {
                            _name: 'CSidebarNavItem',
                            name: 'Dashboard',
                            to: '/dashboard',
                            icon: 'cil-speedometer',
                        },
                            {
                            _name: 'CSidebarNavItem',
                            name: 'Forum',
                            to: '/forum',
                            icon: 'cil-puzzle',
                            badge: {
                                color: 'primary',
                                text: 'NEW'
                            }
                        },
                        ]
                    }
                    ],
        
    }),

    computed: {
        sidebarItems() {
            if (this.userGroup === 'ADMIN') {
                return this.adminMenu
            }
            return this.userMenu
        }
    },

    created() {
        Auth.currentSession()
            .then(data => {
                this.userGroup = data.idToken.payload['cognito:groups'] ? data.idToken.payload['cognito:groups'][0] : 'USER'
            })
    }
}
</script>
